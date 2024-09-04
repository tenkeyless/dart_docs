---
# title: Class modifiers reference
title: 클래스 수정자 참조
description: >-
  허용된 클래스 수정자와 허용되지 않는 클래스 수정자 조합.
prevpage:
  url: /language/class-modifiers
  # title: Class modifiers
  title: 클래스 수정자
nextpage:
  url: /language/concurrency
  # title: Concurrency in Dart
  title: Dart에서의 동시성
---

이 페이지에는 [클래스 수정자](/language/class-modifiers)에 대한 참조 정보가 포함되어 있습니다.

## 유효한 조합 {:#valid-combinations}

클래스 수정자의 유효한 조합과 그에 따른 기능은 다음과 같습니다.

| 선언                 | [생성자][Construct]? | [확장][Extend]? | [구현][Implement]? | [믹스인][Mix in]? | [Exhaustive][Exhaustive]? |
|-----------------------------|----------------|-------------|----------------|-------------|-----------------|
| `class`                     | **Yes**        | **Yes**     | **Yes**        | No          | No              |
| `base class`                | **Yes**        | **Yes**     | No             | No          | No              |
| `interface class`           | **Yes**        | No          | **Yes**        | No          | No              |
| `final class`               | **Yes**        | No          | No             | No          | No              |
| `sealed class`              | No             | No          | No             | No          | **Yes**         |
| `abstract class`            | No             | **Yes**     | **Yes**        | No          | No              |
| `abstract base class`       | No             | **Yes**     | No             | No          | No              |
| `abstract interface class`  | No             | No          | **Yes**        | No          | No              |
| `abstract final class`      | No             | No          | No             | No          | No              |
| `mixin class`               | **Yes**        | **Yes**     | **Yes**        | **Yes**     | No              |
| `base mixin class`          | **Yes**        | **Yes**     | No             | **Yes**     | No              |
| `abstract mixin class`      | No             | **Yes**     | **Yes**        | **Yes**     | No              |
| `abstract base mixin class` | No             | **Yes**     | No             | **Yes**     | No              |
| `mixin`                     | No             | No          | **Yes**        | **Yes**     | No              |
| `base mixin`                | No             | No          | No             | **Yes**     | No              |

{:.table .table-striped .nowrap}

[Construct]: /language/classes#using-constructors
[Extend]: /language/extend
[Implement]: /language/classes#implicit-interfaces
[Mix in]: /language/mixins
[Exhaustive]: /language/branches#exhaustiveness-checking

## 잘못된 조합 {:#invalid-combinations}

특정 수정자 [조합](/language/class-modifiers#combining-modifiers)은 허용되지 않습니다.

| 조합                                   | 이유                                                                                                                                       |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| `base`, `interface`, 및 `final`              | 모든 기능은 동일한 두 가지 기능(`extend` 및 `implement`)을 제어하므로, 상호 배타적(mutually exclusive)입니다.                                                    |
| `sealed` 및 `abstract`                       | 둘 다 구성할 수 없으므로, 함께 사용하면 중복됩니다.                                                                                           |
| `base`, `interface`, 또는 `final`과 함께 `sealed`  | `sealed` 타입은 다른 라이브러리에서 혼합(mixed in), 확장 또는 구현될 수 없으므로, 나열된 수정자와 결합하는 것은 중복됩니다.   |
| `mixin` 및 `abstract`                        | 둘 다 구성할 수 없으므로, 함께 사용하면 중복됩니다.                                                                                          |
| `mixin` 및 `interface`, `final`, 또는 `sealed` | `mixin` 또는 `mixin class` 선언은 혼합(mixed in)되도록 의도된 것이지만, 나열된 수정자는 이를 방지합니다.                                          |
| `enum` 그리고 모든 수정자                      | `enum` 선언은 확장, 구현, 혼합(mixed in)될 수 없으며, 항상 인스턴스화될 수 있으므로, `enum` 선언에는 수정자가 적용되지 않습니다.     |
| `extension type` 그리고 모든 수정자            | `extension type` 선언은 확장되거나 혼합(mixed in)될 수 없으며, 다른 `extension type` 선언에 의해서만 구현될 수 있습니다.               |

{:.table .table-striped .nowrap}
