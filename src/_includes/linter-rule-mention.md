{%- assign split_rules = rules | split: ', ' -%}
린터 규칙{% if split_rules.size > 1 %}들{% endif %}:
{%- for rule in split_rules %}
  [{{rule}}](/tools/linter-rules/{{rule}}){%- unless forloop.last %}, {% endunless %}
{%- endfor %}
{:.linter-rule}