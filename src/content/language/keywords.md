---
# title: Keywords
title: 키워드
# description: Keywords in Dart.
description: Dart의 키워드
toc: false
prevpage:
  url: /language/libraries
  # title: Libraries
  title: 라이브러리
nextpage:
  url: /language/built-in-types
  # title: Built-in types
  title: 빌트인 타입
---

{% assign ckw = '&nbsp;<sup>1</sup>' %}
{% assign bii = '&nbsp;<sup>2</sup>' %}
{% assign unr = '&nbsp;<sup>3</sup>' %}

다음 표는 Dart 언어가 자체적으로 사용하도록 예약한 단어를 나열한 것입니다. 
이러한 단어는 달리 언급되지 않는 한, 식별자로 사용할 수 없습니다. 
허용되는 경우에도, 키워드를 식별자로 사용하면 코드를 읽는 다른 개발자를 혼란스럽게 할 수 있으므로 피해야 합니다. 
식별자 사용에 대해 자세히 알아보려면 용어를 클릭하세요.

<table class="table table-striped">

{% tablerow keyword in keywords cols: 4 %}
<a href="{{keyword.link}}">{{keyword.term}}</a>
{%- case keyword.type %}
{% when 'bit' %}{{bii}}
{% when 'context' %}{{ckw}}
{% when 'unrestricted' %}{{unr}}
{% endcase %}
{% endtablerow %}
</table>

{{ckw}} 이 키워드는 **컨텍스트**에 따라 식별자로 사용될 수 있습니다.

{{bii}} 이 키워드는 타입 이름(클래스, 믹스인, 열거형, 확장 타입 또는 타입 별칭), 확장 이름 또는 import 접두사(prefix)로 사용할 수 없습니다. 다른 모든 상황에서는 식별자로 사용할 수 있습니다.

{{unr}} 이 키워드는 제한 없이 식별자로 사용될 수 있습니다.
