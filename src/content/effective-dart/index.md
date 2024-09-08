---
# title: Effective Dart
title: 효과적인 Dart
# description: >-
#   Best practices for building consistent, maintainable,
#   and efficient Dart libraries.
description: >-
  일관적이고, 유지 관리가 가능하며, 효율적인 Dart 라이브러리를 구축하기 위한 모범 사례입니다.
nextpage:
  url: /effective-dart/style
  # title: Style
  title: 스타일
---

지난 몇 년 동안, 우리는 많은 Dart 코드를 작성했고, 
무엇이 잘 작동하고 무엇이 잘 작동하지 않는지에 대해 많은 것을 배웠습니다. 
우리는 이것을 여러분과 공유하여, 여러분도 일관되고 견고하며 빠른 코드를 작성할 수 있도록 합니다. 
두 가지 포괄적인 주제가 있습니다.

1. **일관성(consistent)을 유지하세요.** 
   - 서식 및 대소문자 구분과 같은 것과 관련하여, 어느 것이 더 나은지에 대한 논쟁은 주관적이며 해결할 수 없습니다.
     우리가 아는 것은 *일관성*이 객관적으로 도움이 된다는 것입니다.

   - 두 개의 코드가 다르게 보인다면, 그것은 어떤 의미 있는 방식으로 *다르기* 때문입니다. 
     코드의 일부가 눈에 띄고 눈길을 끌 때, 그것은 유용한 이유가 있어야 합니다.

2. **간결하게 작성하세요.** 
   - Dart는 친숙하도록 설계되었으므로, 
     C, Java, JavaScript 및 기타 언어와 동일한 명령문과 표현식을 많이 상속합니다. 
     그러나 우리는 이러한 언어가 제공하는 것을 개선할 여지가 많기 때문에 Dart를 만들었습니다. 
     문자열 보간에서 초기화 형식에 이르기까지, 
     여러 기능을 추가하여 의도를 더 간단하고 쉽게 표현할 수 있도록 했습니다.

   - 무언가를 표현하는 방법이 여러 가지라면 일반적으로 가장 간결한 방법을 선택해야 합니다. 
     이는 전체 프로그램을 한 줄로 압축하도록, [코드 골프][code golf]를 해야 한다는 의미는 아닙니다. 
     목표는 *밀도가 높은(dense)* 코드가 아니라 *경제적인(economical)* 코드입니다.

[code golf]: https://en.wikipedia.org/wiki/Code_golf

## 가이드 {:#the-guides}

우리는 쉽게 이해할 수 있도록 가이드라인을 몇 개의 별도 페이지로 나누었습니다.

* **[스타일 가이드][Style Guide]**
  * 이것은 코드의 레이아웃과 구성에 대한 규칙을 정의하거나, 
    적어도 [`dart format`][]에서 처리하지 않는 부분을 정의합니다. 
  * 스타일 가이드는 식별자가 어떻게 포맷되는지 지정합니다: `camelCase`, `using_underscores` 등.

* **[문서 가이드][Documentation Guide]**
  * 이것은 주석 안에 무엇이 들어가는지에 대해 알아야 할 모든 것을 알려줍니다. 
  * 문서 주석과 일반적인 코드 주석 모두.

* **[사용 가이드][Usage Guide]**
  * 이것은 언어 기능을 최대한 활용하여 동작을 구현하는 방법을 알려줍니다. 
  * 문장이나 표현식에 있는 경우, 여기에 설명되어 있습니다.

* **[디자인 가이드][Design Guide]**
  * 이것은 가장 부드러운 가이드이지만 범위가 가장 넓습니다. 
  * 라이브러리를 위한 일관되고 사용 가능한 API를 디자인하는 것에 대해 배운 내용을 다룹니다. 
  * 타입 서명이나 선언에 있는 경우, 이것은 그것을 넘어갑니다.

모든 가이드라인에 대한 링크는 [요약](#summary-of-all-rules)을 참조하세요.

[`dart format`]: /tools/dart-format
[style guide]: /effective-dart/style
[documentation guide]: /effective-dart/documentation
[usage guide]: /effective-dart/usage
[design guide]: /effective-dart/design

## 가이드를 읽는 방법 {:#how-to-read-the-guides}

각 가이드는 몇 개의 섹션으로 나뉩니다. 
섹션에는 가이드라인 리스트가 들어 있습니다. 
각 가이드라인은 다음 단어 중 하나로 시작합니다.

* **DO** 
  * 가이드라인은 항상 따라야 하는 관행을 설명합니다. 
  * 이를 어길 만한 타당한 이유는 거의 없습니다.

* **DON'T** 
  * 가이드라인은 그 반대입니다. 거의 좋은 생각이 아닌 것들입니다. 
  * 우리는 역사적 짐이 적기 때문에 다른 언어만큼 이런 것들이 많지 않기를 바랍니다.

* **PREFER** 
  * 가이드라인은 따라야 *하는* 관행입니다. 
  * 그러나, 그렇지 않은 것이 합리적인 상황이 있을 수 있습니다. 
  * 가이드라인을 무시할 때의 전체적인 의미를 이해해야 합니다.

* **AVOID** 
  * 가이드라인은 "prefer"의 쌍대어입니다. 
  * 해서는 안 되지만, 드물게는 그럴 만한 타당한 이유가 있을 수 있는 것들입니다.

* **CONSIDER** 
  * 가이드라인은 상황, 선례 및 선호도에 따라 따르고 싶거나, 따르고 싶지 않을 수 있는 관행입니다.

일부 가이드라인은 규칙이 적용되지 *않는* **예외**를 설명합니다. 
나열된 예외는 완전하지 않을 수 있습니다. 
다른 경우에는 여전히 판단을 사용해야 할 수도 있습니다.

이것은 당신이 끈을 제대로 묶지 않았다면, 경찰이 당신의 문을 두드릴 것 같습니다. 
상황은 그렇게 나쁘지 않습니다. 
여기의 대부분 가이드라인은 상식이며, 우리는 모두 합리적인 사람들입니다. 
목표는 항상 좋고 읽기 쉽고 유지 관리하기 쉬운 코드입니다.

Dart 분석기는 이러한 가이드라인과 다른 가이드라인을 따르는 훌륭하고 일관된 코드를 작성하는데 도움이 되는 린터를 제공합니다. 
가이드라인을 따르는 데 도움이 되는 하나 이상의 [린터 규칙][lints]가 있는 경우, 
가이드라인은 해당 규칙에 링크됩니다. 
링크는 다음 형식을 사용합니다.

{% render 'linter-rule-mention.md', rules:'unnecessary_getters_setters' %}

Linter를 사용하는 방법을 알아보려면, 
[linter 규칙 활성화][Enabling linter rules] 및 [linter 규칙][lints] 리스트를 참조하세요.

[Enabling linter rules]: /tools/analysis#enabling-linter-rules
[lints]: /tools/linter-rules

## 용어집 {:#glossary}

가이드라인을 간략하게 유지하기 위해, 다양한 Dart 구조를 지칭하는 몇 가지 약어를 사용합니다.

* **라이브러리 멤버 (library member)**
  * 최상위 필드, getter, setter 또는 함수입니다. 
  * 기본적으로 최상위 레벨에서 타입이 아닌 모든 것입니다.

* **클래스 멤버 (class member)**
  * 클래스 내부에서 선언된 생성자, 필드, getter, setter, 함수 또는 연산자입니다. 
  * 클래스 멤버는 인스턴스 또는 정적, 추상 또는 구체적일 수 있습니다.

* **멤버 (member)**
  * 라이브러리 멤버이거나 클래스 멤버입니다.

* **변수 (variable)**
  * 일반적으로 사용되는 변수는 최상위 변수, 매개변수 및 로컬 변수를 나타냅니다. 
  * 정적 또는 인스턴스 필드는 포함되지 않습니다.

* **타입 (type)**
  * 클래스, typedef 또는 열거형과 같은 명명된 타입 선언입니다.

* **속성 (property)**
  * 최상위 변수, getter(클래스 내부 또는 최상위 수준, 인스턴스 또는 정적), setter(동일) 또는 필드(인스턴스 또는 정적)입니다. 
  * 대략 "필드와 유사한" 명명된 구조입니다.

## 모든 규칙의 요약 {:#summary-of-all-rules}

{% comment %}
이하의 내용은 `./dash_site effective-dart` 이 명령어로 자동생성되는 것이기에 번역하지 않습니다.

style.md, documentation.md, usage.md, design.md 내용 중 제목을 번역 후, `./dash_site effective-dart`를 실행하세요.
{% endcomment -%}

{% include './_toc.md' %}
