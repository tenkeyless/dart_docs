---
# title: Dart documentation
title: Dart 문서
# description: Learn to use the Dart language and libraries.
description: Dart 언어와 라이브러리를 사용하는 방법을 알아보세요.
toc: false
---

Dart 문서에 오신 것을 환영합니다! 
이 사이트의 변경 사항 리스트(새 페이지, 새 가이드라인 등)는 [새로운 기능][What's new] 페이지를 참조하세요.

[What's new]: /guides/whats-new

---

## 번역 사이트 개요 {:#translated}

[Dart 사이트](https://dart.dev/)의 한글 번역입니다.

|       |      |
|:-----:|:----|
| Dart 버전 | 3.5.0 |
| 최종 업데이트 | 2024-08-30 |
| 웹사이트 주소 | [dart-docs-kr.web.app](https://dart-docs-kr.web.app/) |
| GitHub 주소 | <https://github.com/tenkeyless/dart_docs/tree/develop> |

{:.table .table-striped}

- Github 사이트에서 태그가 달린 버전 및 날짜에 해당하는 문서를 Docker 통해 구동할 수 있습니다.
- 애플 실리콘의 Docker를 통해서 구동할 수도 있습니다.
- 주기적으로, [dart-lang site-www Github](https://github.com/dart-lang/site-www)에서 문서를 fork 및 업데이트하여, 최신으로 유지할 예정입니다.
- 몇몇 내용은 번역되지 않은 부분이 있을 수 있습니다. 이후, 추가로 번역이 진행될 예정입니다.

---

이 사이트에서 가장 많이 방문한 페이지는 다음과 같습니다.

{% comment %}
To update these cards, edit src/_data/docs_cards.yml.
{% endcomment %}

<div class="card-grid">
{% for card in docs_cards -%}
  {% capture index0Modulo3 %}{{ forloop.index0 | modulo:3 }}{% endcapture %}
  {% capture indexModulo3 %}{{ forloop.index | modulo:3 }}{% endcapture %}
  <div class="card">
    <h2><a href="{{card.url}}">{{card.name}}</a></h2>
    <p>{{card.description}}</p>
  </div>
{% endfor -%}
</div>
