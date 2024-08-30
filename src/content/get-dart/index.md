---
# title: Get the Dart SDK
title: Dart SDK 받기
# description: >-
#   Get the libraries and command-line tools that you need to develop
#   Dart web, command-line, and server apps.
description: >-
  Dart 웹, 명령줄, 서버 앱을 개발하는 데 필요한 라이브러리와 명령줄 도구를 받아보세요.
channel-list: [Stable, Beta, Dev]
js: [{url: '/assets/js/get-dart/install.js', defer: true}]
---

이 페이지에서는 Dart SDK를 다운로드하는 방법을 설명합니다. 
Dart SDK에는 Dart 명령줄, 서버 및 웹 앱을 개발하는 데 필요한 라이브러리와 명령줄 도구가 포함되어 있습니다.

Dart SDK에 대해 자세히 알아보려면, [Dart SDK 개요](/tools/sdk)를 참조하세요.

:::tip
[Flutter SDK를 설치][install-flutter]했거나 설치할 계획이라면, 전체 Dart SDK가 포함됩니다. 
Dart를 별도로 설치할 필요가 없으며, 이 가이드를 건너뛸 수 있습니다.
:::

## 시스템 요구 사항 {:#system-requirements}

Dart는 Dart 코드를 개발하고 실행하기 위해 다음과 같은 하드웨어 아키텍처와 플랫폼 버전을 지원합니다.

{% assign yes = '<span class="material-symbols system-support" style="color: #158477;" aria-label="Supported" title="Supported">verified</span>' %}
{% assign no = '<span class="material-symbols system-support" style="color: #D43324" aria-label="Not supported" title="Not supported">dangerous</span>' %}
{% assign dep = '<span class="material-symbols system-support" style="color: #EF6C00" aria-label="Deprecated" title="Deprecated">error</span>' %}
{% assign na = '<span class="material-symbols system-support" style="color: #DADCE0" aria-label="Does not exist" title="Does not exist">do_not_disturb_on</span>' %}
{% assign macversions = 'macOS의 최신 3가지 버전:<br>' %}
{% for version in macos limit:3 %}
{%- if version.eol == false -%}
{% capture maclinkversion -%}
[{{version.codename}}]({{version.link}}) ({{version.cycle}})
{%- endcapture -%}
{% assign macversions = macversions | append: maclinkversion %}
{%- unless forloop.last -%}{% assign macversions = macversions | append: ', ' %}{% endunless -%}
{%- endif %}
{% endfor %}

| 플랫폼 |   x64   | IA32 (x86) |  Arm32  |  Arm64  | RISC-V (RV64GC) | OS 버전                                                 |
|----------|:-------:|:----------:|:-------:|:-------:|:---------------:|-------------------------------------------------------------|
| Windows  | {{yes}} |  {{dep}}   | {{no}}  | {{yes}} |     {{na}}      | [10], [11][]                                                |
| Linux    | {{yes}} |  {{dep}}   | {{yes}} | {{yes}} |     {{yes}}     | 표준 지원 중인<br> [Debian stable][], [Ubuntu LTS][] |
| macOS    | {{yes}} |  {{no}}    | {{na}}  | {{yes}} |     {{na}}      | {{macversions}}                                             |

{:.table .table-striped}

{{yes}} 모든 채널에서 지원됩니다.<br>
{{dep}} 지원이 deprecated 되었으며, 향후 Dart 릴리스에서 드롭될 수 있습니다.<br>
{{no}} 모든 채널에서 지원되지 않습니다.<br>
{{na}} 운영 체제에서 지원되지 않습니다.<br>

## 설치 옵션 선택 {:#choose-an-installation-option}

stable 채널에서 Dart SDK를 설치하고 업데이트하려면, 다음 옵션 중 하나를 선택하세요.

1. [패키지 관리자 사용](#install) (권장)

2. [Dart Docker 이미지][dart-docker] 사용

3. [Flutter 설치][install-flutter]. 
   [Flutter SDK 설치][install-flutter]를 설치했거나, 설치할 계획이라면 전체 Dart SDK가 포함됩니다. Flutter SDK에는 Flutter의 `bin` 폴더에 [`dart`](/tools/dart-tool) CLI 도구가 포함됩니다.

4. [SDK 아카이브](/get-dart/archive)에서 ZIP 아카이브를 다운로드합니다.

5. [소스에서 SDK 빌드][build-source]

:::warning 공지
{% include './archive/_sdk-terms.md' %}
:::

{% comment %}
NOTE to editors: Keep the zip file link as the last thing in the paragraph,
so it's easy to find (but not more tempting than package managers).
{% endcomment %}

## Dart SDK 설치 {:#install}

Dart SDK를 설치하려면, 개발 플랫폼에 적합한 패키지 관리자를 사용하세요.

Dart SDK를 업그레이드하려면, 동일한 명령을 실행하여 패키지 관리자에서 Dart SDK를 설치하세요.

<ul class="tabs__top-bar">
  <li class="tab-link current" data-tab="tab-sdk-install-windows">Windows</li>
  <li class="tab-link" data-tab="tab-sdk-install-linux">Linux</li>
  <li class="tab-link" data-tab="tab-sdk-install-mac">macOS</li>
</ul>
<div id="tab-sdk-install-windows" class="tabs__content current">

{% include 'install/windows.md' %}

</div>

<div id="tab-sdk-install-linux" class="tabs__content">

{% include 'install/linux.md' %}

</div>

<div id="tab-sdk-install-mac" class="tabs__content">

{% include 'install/macos.md' %}

</div>

## 릴리스 채널 참조 {:#release-channels}

{% for channel in channel-list %}
{% assign chnl = channel | downcase -%}
{% assign current="`[calculating]`{:.build-rev-" | append: chnl | append: "}" %}
{% case chnl %}
{% when 'stable' %}
{% assign verstring = "`x.y.z`" %}
{% assign examples = "`1.24.3` and `2.1.0`" %}
{% assign schedule = "every three months" %}
{% assign version-use = "프로덕션 앱 빌드 및 배포" %}
{% when 'beta' %}
{% assign verstring = "`x.y.z-a.b.beta`" %}
{% assign examples = "`2.8.0-20.11.beta` and `3.3.0-205.1.beta`" %}
{% assign verdesc = "pre-release" %}
{% assign schedule = "once a month" %}
{% assign version-use = "향후 stable 버전과 앱의 호환성 테스트" %}
{% when 'dev' %}
{% assign verstring = "`x.y.z-a.b.dev`" %}
{% assign examples = "`2.8.0-20.11.dev` and `3.2.12-15.33.dev`" %}
{% assign verdesc = "development" %}
{% assign schedule = "twice a week" %}
{% assign version-use = "최근 수정 사항 및 실험적 기능 테스트" %}
{% endcase %}

<!-- ### {{channel}} channel -->
### {{channel}} 채널

Dart는 {{schedule}}에 대한 *{{chnl}}* 채널에 새 릴리스를 게시합니다.
현재 {{chnl}} 버전은 {{current}}입니다.

{{version-use}}에 **{{chnl}}** 채널 릴리스를 사용합니다.

**{{channel}}** 채널 릴리스 버전 문자열은 {{verstring}} 형식을 따릅니다.

* `x` : 메이저 버전
* `y` : 마이너 버전
* `z` : 패치 버전
{%- if chnl != 'stable' %}
* `a` : {{verdesc}} 버전
* `b` : {{verdesc}} 패치 버전
{% endif %}

{{chnl}} 채널 버전 문자열의 예로는 {{examples}}가 있습니다.

{{chnl}} 채널 릴리스를 설치하려면,
{%- if chnl != 'stable' %}
[SDK를 zip 파일][dl-sdk]로 다운로드합니다.
{%- else %}
[이 페이지의 지침](#install)을 따르세요.
{% endif %}

{% endfor -%}

[build-source]: {{site.repo.dart.sdk}}/wiki/Building
[dart-docker]: https://hub.docker.com/_/dart
[dl-sdk]: /get-dart/archive
[install-flutter]: {{site.flutter-docs}}/get-started/install
[10]: https://www.microsoft.com/en-us/software-download/windows10%20
[11]: https://www.microsoft.com/en-us/software-download/windows11
[Debian stable]: https://www.debian.org/releases
[Ubuntu LTS]: https://wiki.ubuntu.com/Releases
