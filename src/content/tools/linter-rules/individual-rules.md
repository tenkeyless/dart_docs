---
pagination:
  data: linter_rules
  size: 1
  alias: lint
  addAllPagesToCollections: true
show_breadcrumbs: true
underscore_breaker_titles: true
eleventyComputed:
  permalink: "/tools/linter-rules/{{lint.name}}.html"
  title: "{{ lint.name }}"
  description: "Learn more about the {{ lint.name }} linter rule."
skipFreshness: true
---

{{lint.description}}

{% if lint.sinceDartSdk == "Unreleased" or lint.sinceDartSdk contains "-wip" -%}
<!-- _This rule is currently **experimental** and not yet available in a stable SDK._ -->
_이 규칙은 현재 **experimental**이며, 아직 stable SDK에서 사용할 수 없습니다._
{% elsif lint.state == "removed" -%}
<!-- _This rule has been removed as of the latest Dart releases._ -->
_이 규칙은 최신 Dart 릴리스부터 제거되었습니다._
{% elsif lint.state != "stable" -%}
<!-- _This rule is currently **{{lint.state}}** and available as of Dart {{lint.sinceDartSdk}}._ -->
_이 규칙은 현재 **{{lint.state}}**이며, Dart {{lint.sinceDartSdk}}부터 사용할 수 있습니다._
{% else -%}
<!-- _This rule is available as of Dart {{lint.sinceDartSdk}}._ -->
_이 룰은 Dart {{lint.sinceDartSdk}}에서 사용가능합니다._
{% endif -%}

{% if lint.sets != empty -%}

{% assign rule_sets = "" -%}

{% for set in lint.sets -%}

{% if set == "core" or set == "recommended" -%}
{% assign set_link = "lints" %}
{% elsif set == "flutter" -%}
{% assign set_link = "flutter_lints" %}
{% else -%}
{% assign set_link = set %}
{% endif -%}

{%- capture rule_set -%}
[{{set}}](/tools/linter-rules#{{set_link}}){% if forloop.last == false %},{% endif %}
{% endcapture -%}

{%- assign rule_sets = rule_sets | append: rule_set -%}
{% endfor -%}

<em>Rule sets: {{ rule_sets }}</em>
{% endif -%}

{% if lint.fixStatus == "hasFix" %}
<!-- _This rule has a [quick fix](/tools/linter-rules#quick-fixes) available._ -->
_이 규칙에는 [빠른 수정](/tools/linter-rules#quick-fixes)이 있습니다._
{% endif %}

{% if lint.incompatible != empty -%}
{% assign incompatible_rules = "" -%}

{% for incompatible in lint.incompatible -%}

{%- capture incompatible_rule -%}
[{{incompatible}}](/tools/linter-rules/{{incompatible}}){% if forloop.last == false %},{% endif %}
{% endcapture -%}

{% assign incompatible_rules = incompatible_rules | append: incompatible_rule -%}
{% endfor -%}

<em>호환되지 않는 규칙: {{ incompatible_rules }}</em>
{% endif -%}

## 세부사항 {:#details}

{{lint.details}}

## 사용 {:#usage}

`{{lint.name}}` 규칙을 활성화하려면, 
[`analysis_options.yaml`](/tools/analysis) 파일의 **linter > rules** 아래에, 
`{{lint.name}}`을 추가합니다.

```yaml title="analysis_options.yaml"
linter:
  rules:
    - {{lint.name}}
```
