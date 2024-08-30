---
# title: Comments
title: 주석
# description: The different comment types in Dart.
description: Dart의 다양한 주석 타입.
prevpage:
  url: /language/operators
  # title: Operators
  title: 연산자
nextpage:
  url: /language/metadata
  # title: Metadata
  title: 메타데이터
---

Dart는 단일 줄 주석, 다중 줄 주석, 문서 주석을 지원합니다.

## 단일 줄 주석 {:#single-line-comments}

한 줄 주석은 `//`로 시작합니다. 
`//`와 줄 끝 사이의 모든 것은 Dart 컴파일러에 의해 무시됩니다.

<?code-excerpt "misc/lib/language_tour/comments.dart (single-line-comments)"?>
```dart
void main() {
  // TODO: AbstractLlamaGreetingFactory로 리팩토링할까요?
  print('Welcome to my Llama farm!');
}
```

## 다중 줄 주석 {:#multi-line-comments}

여러 줄 주석은 `/*`로 시작하고 `*/`로 끝납니다. 
`/*`와 `*/` 사이의 모든 것은 Dart 컴파일러에 의해 무시됩니다.
(주석이 문서 주석인 경우 제외, 다음 섹션 참조) 
여러 줄 주석은 중첩될 수 있습니다.

<?code-excerpt "misc/lib/language_tour/comments.dart (multi-line-comments)"?>
```dart
void main() {
  /*
   * 이건 정말 많은 작업이에요. 닭을 키우는 것을 고려해보세요.

  Llama larry = Llama();
  larry.feed();
  larry.exercise();
  larry.clean();
   */
}
```

## 문서 주석 {:#documentation-comments}

문서 주석은 `///` 또는 `/**`로 시작하는 여러 줄 또는 한 줄 주석입니다. 
연속된 줄에 `///`를 사용하면 여러 줄 문서 주석과 동일한 효과가 있습니다.

문서 주석 내부에서 분석기는 대괄호로 묶이지 않은 모든 텍스트를 무시합니다. 
대괄호를 사용하면 클래스, 메서드, 필드, 최상위 변수, 함수 및 매개변수를 참조할 수 있습니다. 
대괄호 안의 이름은 문서화된 프로그램 요소의 어휘 범위에서 확인됩니다.

다음은 다른 클래스와 인수에 대한 참조가 있는 문서 주석의 예입니다.

<?code-excerpt "misc/lib/language_tour/comments.dart (doc-comments)"?>
```dart
/// 길들여진 남미 낙타과 동물(Lama glama).
///
/// 안데스 문화권에서는 라마를 스페인 정복 이전부터 
/// 고기와 짐을 나르는 동물로 이용해 왔습니다.
///
/// 다른 동물과 마찬가지로 라마도 먹어야 하므로, [Food]을 [feed] 것을 잊지 마세요.
class Llama {
  String? name;

  /// 라마에게 [food]를 줍니다.
  ///
  /// 전형적인 라마는 일주일에 건초 한 묶음을 먹습니다.
  void feed(Food food) {
    // ...
  }

  /// [activity]로 라마를 [timeLimit]분 동안 운동시킵니다.
  void exercise(Activity activity, int timeLimit) {
    // ...
  }
}
```

클래스의 생성된 문서에서, `[feed]`는 `feed` 메서드에 대한 문서로 연결되는 링크가 되고, 
`[Food]`는 `Food` 클래스에 대한 문서로 연결되는 링크가 됩니다.

Dart 코드를 구문 분석하고 HTML 문서를 생성하려면, 
Dart의 문서 생성 도구인 [`dart doc`](/tools/dart-doc)을 사용할 수 있습니다. 
생성된 문서의 예는 [Dart API 문서]({{site.dart-api}}/{{site.sdkInfo.channel}})를 참조하세요. 
주석을 구성하는 방법에 대한 조언은, [효과적인 Dart: 문서](/effective-dart/documentation)를 참조하세요.