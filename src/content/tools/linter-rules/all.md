---
# title: All linter rules
title: 모든 린터 규칙
# description: Auto-generated configuration enabling all linter rules.
description: 모든 린터 규칙을 활성화하는 자동 생성 구성입니다.
toc: false
show_breadcrumbs: true
---

다음은 버전 `{{site.sdkInfo.version}}` 기준으로, 
Dart SDK에서 사용 가능한 모든 린터 규칙의 자동 생성 리스트입니다. 
[`analysis_options.yaml`](/tools/analysis) 파일에 추가하고, 적합하다고 생각되는 대로 조정하세요.

{% assign sorted_lints = linter_rules | sort: "name" %}

```yaml title="analysis_options.yaml"
linter:
  rules:
    {% for lint in sorted_lints %}
    {%- if lint.sinceDartSdk != "Unreleased" and lint.sinceDartSdk not contains "-wip" and lint.state != "removed" and lint.state != "internal" -%}
    - {{lint.name}}
    {% endif -%}
    {%- endfor %}
```
