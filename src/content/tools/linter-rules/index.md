---
# title: Linter rules
title: 린터 규칙
# description: Details about the Dart linter and its style rules you can choose.
description: Dart linter와 선택할 수 있는 스타일 규칙에 대한 세부 정보입니다.
show_breadcrumbs: true
---

Dart 린터를 사용하여, Dart 코드에서 발생할 수 있는 문제를 식별합니다. 
IDE를 통해 또는 [`dart analyst`](/tools/dart-analyze) 명령으로 린터를 사용할 수 있습니다. 
개별 린터 규칙을 활성화 및 비활성화하는 방법에 대한 자세한 내용은, 
[analyzer 문서][analyzer documentation]의 [개별 규칙 섹션][individual rules sections]을 참조하세요.

[individual rules sections]: /tools/analysis#individual-rules
[analyzer documentation]: /tools/analysis

이 페이지에는 모든 린터 규칙이 나열되어 있으며, 
각 규칙을 언제 사용해야 할지, 어떤 코드 패턴이 규칙을 트리거하는지, 
어떻게 코드를 수정할 수 있는지 등의 세부 정보가 나와 있습니다.

:::tip
린터 규칙(때때로 _린트(lints)_ 라고 함)은 거짓 양성(false positives)을 가질 수 있으며, 
모두 서로 일치하지는 않습니다. 
예를 들어, 일부 규칙은 일반 Dart 패키지에 더 적합하고, 
다른 규칙은 Flutter 앱에 맞게 설계되었습니다.
:::

<a id="predefined-rule-sets"></a>
## 세트 {:#sets}

호환되는 린터 규칙을 개별적으로 선택할 필요가 없도록, 
다음의 패키지가 제공하는 린터 규칙 세트로 시작하는 것을 고려하세요.

<a id="lints"></a>

[lints][]
: Dart 팀에서 큐레이팅한 두 가지 규칙 세트가 포함되어 있습니다. 
  최소한 `core` 규칙 세트를 사용하는 것이 좋습니다. 
  이 규칙 세트는 [pub.dev]({{site.pub}})에 업로드된 패키지를 [scoring]({{site.pub}}/help/scoring)할 때 사용됩니다. 
  아니면, `recommended` 규칙 세트를 사용하는 것이 더 좋습니다. 
  이 규칙 세트는 `core`의 슈퍼세트로, 추가 문제를 식별하고 스타일과 형식을 적용합니다. 
  Flutter 코드를 작성하는 경우, 
  `lints`를 기반으로 하는 [`flutter_lints`](#flutter_lints) 패키지의 규칙 세트를 사용하세요.

<a id="flutter_lints"></a>

[flutter_lints][]
: Flutter 팀에서 Flutter 앱, 패키지 및 플러그인에서 사용하도록 권장하는, 
  `flutter` 규칙 세트가 포함되어 있습니다. 
  이 규칙 세트는 [`recommended`](#lints) 세트의 상위 세트이며, 
  이 세트 자체가 [pub.dev]({{site.pub}})에 업로드된 패키지의 
  [score]({{site.pub}}/help/scoring)를 부분적으로 결정하는 [`core`](#lints) 세트의 상위 세트입니다.

[lints]: {{site.pub-pkg}}/lints
[flutter_lints]: {{site.pub-pkg}}/flutter_lints

특정 규칙 세트를 사용하는 방법을 알아보려면, 
[린터 규칙 활성화 및 비활성화][enabling and disabling linter rules]에 대한 설명서를 방문하세요.

더 많은 사전 정의된 규칙 세트를 찾으려면, 
pub.dev의 [`#lints` 토픽]({{site.pub-pkg}}?q=topic:lints)을 확인하세요.

[enabling and disabling linter rules]: /tools/analysis#enabling-linter-rules

<a id="maturity-levels"></a>
## 상태 {:#status}

각 규칙에는 상태 또는 성숙도가 있습니다.

**Stable**
: 이러한 규칙은 사용하기에 안전하며, 최신 버전의 Dart 언어에서 작동하는 것으로 검증되었습니다. 
  모든 규칙은 experimental, deprecated 또는 removed로 표시되지 않는 한 stable로 간주됩니다.

**Experimental**
: 이러한 규칙은 아직 평가 중이며 안정화되지 않을 수 있습니다. 주의해서 사용하고 발견한 문제는 보고하세요.

**Deprecated**
: 이러한 규칙은 더 이상 사용하도록 제안되지 않으며, 향후 Dart 릴리스에서 제거될 수 있습니다.

**Removed**
: 이러한 규칙은 최신 stable Dart 릴리스에서 이미 제거되었습니다.

## 빠른 수정 {:#quick-fixes}

일부 규칙은 빠른 수정을 사용하여 자동으로 수정할 수 있습니다. 
빠른 수정은 린터 규칙에서 보고된 문제를 수정하는 것을 목표로 하는 자동화된 편집입니다.

규칙에 빠른 수정이 있는 경우, 
[`dart fix`](/tools/dart-fix)를 사용하거나, 
[Dart 지원 편집기](/tools#editors)를 사용하여 적용할 수 있습니다. 
자세한 내용은 [분석 문제에 대한 빠른 수정][Quick fixes for analysis issues]을 참조하세요.

[Quick fixes for analysis issues]: https://medium.com/dartlang/quick-fixes-for-analysis-issues-c10df084971a

## 규칙 {:#rules}

다음은 모든 linter 규칙의 인덱스와 해당 기능에 대한 간략한 설명입니다. 
각 규칙에 대해 자세히 알아보려면 해당 이름을 클릭하세요.

Dart `{{site.sdkInfo.version}}`의 모든 린터 규칙이 포함된, 
자동 생성 리스트는 [모든 린터 규칙](/tools/linter-rules/all)을 확인하세요.

{% render 'linter-rules-section.md', linter_rules:linter_rules %}

[Dart style guide]: /effective-dart/style
