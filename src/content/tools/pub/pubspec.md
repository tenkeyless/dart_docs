---
# title: The pubspec file
title: pubspec 파일
# description: Reference guide for the fields in pubspec.yaml.
description: pubspec.yaml의 필드에 대한 참조 가이드입니다.
---

모든 [pub 패키지](/guides/packages)에는 [종속성](/tools/pub/glossary#dependency)을 지정할 수 있도록 일부 메타데이터가 필요합니다. 
다른 사람과 공유되는 Pub 패키지는 사용자가 검색할 수 있도록 다른 정보도 제공해야 합니다. 
이 모든 메타데이터는 패키지의 _pubspec:_ 에 들어가며, 
`pubspec.yaml`이라는 파일이며 [YAML](https://yaml.org/) 언어로 작성됩니다.

{% comment %}
PENDING: acknowledge the existence of pubspec.lock files.
{% endcomment %}

## 지원되는 필드 {:#supported-fields}

pubspec에는 다음과 같은 필드가 있을 수 있습니다.

`name`
: 모든 패키지에 필요합니다.
  [_자세히 알아보기_](#name)

`version`
: pub.dev 사이트에 호스팅된 패키지에 필요합니다.
  [_자세히 알아보기_](#version)

`description`
: pub.dev 사이트에 호스팅된 패키지에 필요합니다.
  [_자세히 알아보기_](#description)

`homepage`
: 선택 사항. 패키지의 홈페이지(또는 소스 코드 저장소)를 가리키는 URL입니다.
  [_자세히 알아보기_](#homepage)

`repository`
: 선택 사항. 패키지의 소스 코드 저장소를 가리키는 URL입니다.
  [_자세히 알아보기_](#repository)

`issue_tracker`
: 선택 사항. 패키지의 이슈 추적기를 가리키는 URL입니다.
  [_자세히 알아보기_](#issue-tracker)

`documentation`
: 선택 사항. 패키지에 대한 문서를 가리키는 URL입니다.
  [_자세히 알아보기_](#documentation)

`dependencies`
: 패키지에 종속성이 없는 경우 생략할 수 있습니다.
  [_자세히 알아보기_](#dependencies)

`dev_dependencies`
: 패키지에 개발 종속성이 없는 경우 생략할 수 있습니다.
  [_자세히 알아보기_](#dependencies)

`dependency_overrides`
: 종속성을 재정의할 필요가 없으면 생략할 수 있습니다.
  [_자세히 알아보기_](#dependencies)

`environment`
: Dart 2부터 필수입니다.
  [_자세히 알아보기_](#sdk-constraints)

`executables`
: 선택 사항. 패키지의 executables을 PATH에 넣는 데 사용됩니다.
  [_자세히 알아보기_](#executables)

`platforms`
: 선택 사항. pub.dev 사이트에서 지원되는 플랫폼을 명시적으로 선언하는 데 사용됩니다.
  [_자세히 알아보기_](#platforms)

`publish_to`
: 선택 사항. 패키지를 게시할 위치를 지정하세요.
  [_자세히 알아보기_](#publish_to)

`funding`
: 선택 사항. 사용자가 패키지 개발을 후원할 수 있는 URL 목록입니다.
  [_자세히 알아보기_](#funding)

`false_secrets`
: 선택 사항. 비밀 유출 가능성에 대한 사전 게시 검색을 수행할 때 무시할 파일을 지정합니다.
  [_자세히 알아보기_](#false_secrets)
  
`screenshots`
: 선택 사항. [pub.dev 사이트]({{site.pub}})에 표시할 스크린샷 파일 리스트를 지정하세요.
  [_자세히 알아보기_](#screenshots)

`topics`
: 선택 사항. 패키지 주제 리스트.
  [_자세히 알아보기_](#topics)

`ignored_advisories`
: 선택 사항. 무시된 보안 권고 리스트.
  [_자세히 알아보기_](#ignored_advisories)

Pub은 다른 모든 필드를 무시합니다.

:::flutter-note
[Flutter 앱]({{site.flutter}})용 Pubspec에는 환경 구성 및 assets 관리를 위한, 
[추가 필드]({{site.flutter-docs}}/development/tools/pubspec)가 있을 수 있습니다.
:::

커스텀 필드를 추가하는 경우, 향후 pubspec 필드와 충돌하지 않는 고유한 이름을 지정합니다. 
예를 들어, `bugs`를 추가하는 대신, `my_pkg_bugs`라는 이름의 필드를 추가할 수 있습니다.

## 예제 {:#example}

간단하지만 완전한 pubspec은 다음과 같습니다.

```yaml
name: newtify
description: >-
  Have you been turned into a newt?  Would you like to be?
  This package can help. It has all of the
  newt-transmogrification functionality you have been looking
  for.
version: 1.2.3
homepage: https://example-pet-store.com/newtify
documentation: https://example-pet-store.com/newtify/docs

environment:
  sdk: '^3.2.0'
  
dependencies:
  efts: ^2.0.4
  transmogrify: ^0.4.0
  
dev_dependencies:
  test: '>=1.15.0 <2.0.0'
```


## 세부사항 {:#details}

이 섹션에는 각 pubspec 필드에 대한 자세한 정보가 있습니다.

### 이름 {:#name}

모든 패키지에는 이름이 필요합니다. 
다른 패키지가 당신의 패키지를 지칭하는 방식이며, 
당신이 패키지를 게시할 경우 세상에 표시되는 방식입니다.

이름은 모두 소문자여야 하며, 단어를 구분하기 위해 밑줄을 사용해야 합니다. (`just_like_this`)
기본 라틴 문자와 아라비아 숫자만 사용합니다. (`[a-z0-9_]`) 
또한, 이름이 유효한 Dart 식별자인지 확인하세요.
(숫자로 시작하지 않고 [예약어](/language/keywords)가 아닌지 확인하세요)

명확하고 간결하며 이미 사용 중이 아닌 이름을 선택하세요. 
[pub.dev 사이트]({{site.pub-pkg}})에서 패키지를 빠르게 검색하여, 
다른 패키지에서 당신의 이름을 사용하지 않는지 확인하는 것이 좋습니다.

### 버전 {:#version}

모든 패키지에는 버전이 있습니다. 
pub.dev 사이트에서 패키지를 호스팅하려면 버전 번호가 필요하지만, 로컬 전용 패키지의 경우 생략할 수 있습니다. 
생략하면, 패키지의 버전이 암묵적으로 `0.0.0`으로 지정됩니다.

버전 관리란 코드를 재사용하면서 빠르게 진화시키는 데 필요합니다. 
버전 번호는 `0.2.43`과 같이 점으로 구분된 세 개의 숫자입니다. 
또한 선택적으로 빌드(`+1`, `+2`, `+hotfix.oopsie`) 또는 
사전 릴리스(`-dev.4`, `-alpha.12`, `-beta.7`, `-rc.5`) 접미사를 붙일 수 있습니다.

패키지를 게시할 때마다 특정 버전으로 게시합니다. 
이 작업이 완료되면 밀폐된(sealed) 상태로 간주합니다. 더 이상 건드릴 수 없습니다. 
더 많은 변경을 하려면, 새 버전이 필요합니다.

버전을 선택할 때는, [시맨틱 버전 관리][semantic versioning]를 따릅니다.

[semantic versioning]: https://semver.org/spec/v2.0.0-rc.1.html

### 설명 {:#description}

개인 패키지의 경우 선택 사항이지만, 패키지를 게시하려면 영어로 된 설명을 제공해야 합니다. 
설명은 비교적 짧아야 합니다. (60~180자) 
일반 독자에게 패키지에 대해 알고 싶어할 만한 내용을 알려줘야 합니다.

설명을 패키지의 판매 전략으로 생각하세요. 
사용자는 [패키지를 탐색할 때]({{site.pub-pkg}}) 설명을 봅니다. 
설명은 일반 텍스트입니다. 마크다운이나 HTML이 없습니다.

### 홈페이지 {:#homepage}

이것은 당신의 패키지에 대한 웹사이트를 가리키는 URL이어야 합니다. 
[호스팅된 패키지](/tools/pub/dependencies#hosted-packages)의 경우, 이 URL은 패키지 페이지에서 링크됩니다. 
`homepage`를 제공하는 것은 선택 사항이지만, 홈페이지 또는 `repository`(또는 둘 다)를 *제공해 주세요*. 
사용자가 당신의 패키지가 어디에서 왔는지 이해하는 데 도움이 됩니다.

### 저장소 {:#repository}

선택 사항인 `repository` 필드에는 패키지의 소스 코드 리포지토리 URL이 포함되어야 합니다.
(예: `https://github.com/<user>/<repository>`) 
패키지를 pub.dev 사이트에 게시하면, 패키지 페이지에 리포지토리 URL이 표시됩니다. 
`repository`를 제공하는 것은 선택 사항이지만, 
`repository` 또는 `homepage`(또는 둘 다)를 *제공해 주세요.* 
사용자가 패키지의 출처를 이해하는 데 도움이 됩니다.

### 이슈 트래커 {:#issue-tracker}

선택 사항인 `issue_tracker` 필드에는 기존 버그를 보고 새 버그를 제출할 수 있는, 
패키지의 이슈 트래커에 대한 URL이 포함되어야 합니다. 
pub.dev 사이트는 이 필드의 값을 사용하여, 
각 패키지의 이슈 트래커에 대한 링크를 표시하려고 시도합니다. 
`issue_tracker`가 없지만 `repository`가 있고 GitHub을 가리키는 경우, 
pub.dev 사이트는 기본 이슈 트래커(`https://github.com/<user>/<repository>/issues`)를 사용합니다.

### 문서화 {:#documentation}

일부 패키지에는 메인 홈페이지와 Pub에서 생성된 API 참조와 별도로, 문서를 호스팅하는 사이트가 있습니다. 
패키지에 추가 문서가 있는 경우, 해당 URL이 있는 `documentation:` 필드를 추가하면, 
pub에서 패키지 페이지에 이 문서에 대한 링크를 표시합니다.

### 종속성 {:#dependencies}

[종속성](/tools/pub/glossary#dependency)은 pubspec의 *존재 이유*입니다. 
이 섹션에서는 패키지가 작동하기 위해 필요한 각 패키지를 나열합니다.

종속성은 두 가지 타입 중 하나에 속합니다. 
_일반 종속성_ 은 `dependencies:`에 나열됩니다. 
이는 패키지를 사용하는 모든 사람에게 필요한 패키지입니다. 
패키지 자체의 개발 단계에서만 필요한 종속성은 `dev_dependencies`에 나열됩니다.

개발 프로세스 중에 종속성을 일시적으로 재정의해야 할 수도 있습니다. 
`dependency_overrides`를 사용하여 그렇게 할 수 있습니다.

자세한 내용은 [패키지 종속성](/tools/pub/dependencies)을 참조하세요.

### Executables {:#executables}

패키지는 하나 이상의 스크립트를 명령줄에서 직접 실행할 수 있는 executables로 노출할 수 있습니다. 
스크립트를 공개적으로 사용 가능하게 하려면, `executables` 필드에 나열합니다. 
항목은 키/값 쌍으로 나열됩니다.

```plaintext
<name-of-executable>: <Dart-script-from-bin>
```

예를 들어, 다음 pubspec 항목은 두 개의 스크립트를 나열합니다.

```yaml
executables:
  slidy: main
  fvm:
```

`dart pub global activate`를 사용하여 패키지를 활성화한 후, 
`slidy`를 입력하면 `bin/main.dart`가 실행됩니다. 
`fvm`을 입력하면 `bin/fvm.dart`가 실행됩니다. 
값을 지정하지 않으면 키에서 추론됩니다.

자세한 내용은 [pub global](/tools/pub/cmd/pub-global#running-a-script-from-your-path)을 참조하세요.

### 플랫폼 {:#platforms}

[패키지를 게시][publish a package]할 때, pub.dev는 패키지가 지원하는 플랫폼을 자동으로 감지합니다. 
이 플랫폼 지원 목록이 올바르지 않은 경우, 
`platforms`를 사용하여 패키지가 지원하는 플랫폼을 명시적으로 선언합니다.

예를 들어, 다음 `platforms` 항목은 pub.dev가 패키지를 
Android, iOS, Linux, macOS, Web 및 Windows를 지원하는 것으로 나열하게 합니다.

```yaml
# 이 패키지는 아래 나열된 모든 플랫폼을 지원합니다.
platforms:
  android:
  ios:
  linux:
  macos:
  web:
  windows:
```

다음은 패키지가 Linux와 macOS만 지원한다는 것을 선언하는 예입니다(예를 들어 Windows는 지원하지 않음):

```yaml
# 이 패키지는 Linux와 macOS만 지원합니다.
platforms:
  linux:
  macos:
```

:::note If you use Flutter
Flutter 플러그인 플랫폼 지원은 기본적으로 [플러그인 선언][plugin declarations]에서 파생됩니다.

플러그인 선언과 실제 플랫폼 지원 사이에 불일치가 있는 경우, 
최상위 `platforms` 선언을 계속 사용할 수 있으며, 
플랫폼 지원을 결정할 때 Flutter 플러그인 선언보다 우선합니다.
:::

:::version-note
Dart 2.16에서 `platforms` 항목에 대한 지원이 추가되었습니다.
:::

[publish a package]: /tools/pub/publishing
[plugin declarations]: {{site.flutter-docs}}/development/packages-and-plugins/developing-packages#plugin-platforms


### Publish_to {:#publish_to}

기본값은 [pub.dev 사이트]({{site.pub}})를 사용합니다. 
패키지가 게시되지 않도록 하려면 `none`을 지정합니다. 
이 설정은 게시할 [커스텀 pub 패키지 서버](/tools/pub/custom-package-repositories)를 지정하는 데 사용할 수 있습니다.

```yaml
publish_to: none
```


### Funding {:#funding}

패키지 작성자는 `funding` 속성을 사용하여, 
사용자가 패키지 개발에 자금을 지원하는 방법에 대한 정보를 제공하는 URL 목록을 지정할 수 있습니다. 
예를 들어:

```yaml
funding:
 - https://www.buymeacoffee.com/example_user
 - https://www.patreon.com/some-account
```

[pub.dev]({{site.pub}})에 게시된 경우 링크가 패키지 페이지에 표시됩니다. 
이는 사용자가 종속성 개발에 자금을 지원하는 것을 목표로 합니다.


### False_secrets {:#false_secrets}

[패키지 게시][publish a package]를 시도하면, 
pub에서 비밀 자격 증명, API 키 또는 암호화 키의 잠재적 누출을 검색합니다. 
pub에서 게시될 파일에서 잠재적 누출을 감지하면, pub에서 경고하고 패키지를 게시하지 않습니다.

누출 감지는 완벽하지 않습니다. 
거짓 양성을 피하기 위해, 
pubspec의 `false_secrets`에서 [`gitignore` 패턴][`gitignore` patterns]을 사용하여, 
허용 리스트를 만들어 특정 파일에서 누출을 검색하지 않도록 pub에 지시할 수 있습니다.

[`gitignore` patterns]: https://git-scm.com/docs/gitignore#_pattern_format

예를 들어, 다음 항목은 pub가 `lib/src/hardcoded_api_key.dart` 파일과 `test/localhost_certificates/` 디렉터리의 모든 `.pem` 파일에서 누수를 찾지 않도록 합니다.

[publish a package]: /tools/pub/publishing

```yaml
false_secrets:
 - /lib/src/hardcoded_api_key.dart
 - /test/localhost_certificates/*.pem
```

`gitignore` 패턴을 슬래시(`/`)로 시작하면, 
해당 패턴이 패키지 루트 디렉토리를 기준으로 간주되도록 합니다.

:::warning
**누출 감지에 의존하지 마세요.** 
제한된 패턴 세트를 사용하여 일반적인 실수를 감지합니다. 
자격 증명을 관리하고, 우발적인 누출을 방지하고, 
우발적으로 누출된 자격 증명을 취소하는 것은 당신의 책임입니다.
:::

:::version-note
Dart 2.15에서는 `false_secrets` 필드에 대한 지원이 추가되었습니다.
:::

### Screenshots {:#screenshots}

패키지는 pub.dev 페이지에 표시된 스크린샷을 사용하여, 
위젯이나 기타 시각적 요소를 선보일 수 있습니다. 
패키지에 표시할 스크린샷을 지정하려면 `screenshots` 필드를 사용합니다.

패키지는 `screenshots` 필드에 최대 10개의 스크린샷을 나열할 수 있습니다. 
이 섹션에 로고나 기타 브랜딩 이미지를 포함하지 마십시오. 
각 스크린샷에는 하나의 `description`과 하나의 `path`가 포함됩니다. 
`description`은 스크린샷이 묘사하는 내용을 최대 160자로 설명합니다. 예:

```yaml
screenshots:
  - description: 'This screenshot shows the transformation of a number of bytes 
  to a human-readable expression.'
    path: path/to/image/in/package/500x500.webp
  - description: 'This screenshot shows a stack trace returning a human-readable
  representation.'
    path: path/to/image/in/package.png
```

Pub.dev는 스크린샷을 다음 사양으로 제한합니다.

- 파일 크기: 이미지당 최대 4MB.
- 파일 유형: `png`, `jpg`, `gif` 또는 `webp`.
- 정적 및 애니메이션 이미지 모두 허용됩니다.

스크린샷 파일을 작게 유지하세요. 패키지를 다운로드할 때마다 모든 스크린샷 파일이 포함됩니다.

Pub.dev는 첫 번째 스크린샷에서 패키지의 썸네일 이미지를 생성합니다. 
이 스크린샷이 애니메이션을 사용하는 경우, pub.dev는 첫 번째 프레임을 사용합니다.

 
### Topics {:#topics}

패키지 작성자는 `topics` 필드를 사용하여 패키지를 분류할 수 있습니다. 
주제는 pub.dev에서 필터를 사용하여 검색하는 동안 발견 가능성을 높이는 데 사용할 수 있습니다. 
Pub.dev는 패키지 페이지와 검색 결과에 주제를 표시합니다.

필드는 이름 리스트로 구성됩니다. 예를 들어:

```yaml
topics:
  - network
  - http
```

Pub.dev에서는 토픽이 다음 사양을 따라야 합니다.

- 각 패키지에 최대 5개의 토픽을 태그합니다.
- 다음 요구 사항에 따라 토픽 이름을 작성합니다.
- 2~32자를 사용합니다.
- 소문자 영숫자 또는 하이픈(`a-z`, `0-9`, `-`)만 사용합니다.
- 두 개의 연속된 하이픈(`--`)은 사용하지 않습니다.
- 소문자 알파벳 문자(`a-z`)로 이름을 시작합니다.
- 영숫자 문자(`a-z` 또는 `0-9`)로 끝냅니다.

토픽을 선택할 때 [기존 토픽]({{site.pub}}/topics)이 관련이 있는지 고려합니다. 
기존 토픽으로 태그를 지정하면 사용자가 패키지를 발견하는 데 도움이 됩니다.

### Ignored_advisories {:#ignored_advisories}

패키지에 보안 권고의 영향을 받는 종속성이 있는 경우, 
pub는 종속성 해결 중에 권고에 대해 경고합니다. 
패키지 작성자는 `ignored_advisories` 필드를 패키지와 관련이 없는 트리거된 권고의 허용 리스트로 사용할 수 있습니다.

권고에 대한 경고를 억제하려면, 권고 식별자를 `ignored_advisories` 목록에 추가합니다. 예:

```yaml
name: myapp
dependencies:
  foo: ^1.0.0
ignored_advisories:
 - GHSA-4rgh-jx4f-qfcq
```

자세한 내용은 [보안 권고 사항](/tools/pub/security-advisories)을 확인하세요.

### SDK 제약 {:#sdk-constraints}

패키지는 지원하는 종속성 버전을 나타낼 수 있지만, 
패키지에는 또 다른 암묵적 종속성인 Dart 플랫폼 자체가 있습니다. 
Dart 플랫폼은 시간이 지남에 따라 진화하며, 
패키지는 특정 버전의 플랫폼에서만 작동할 수 있습니다.

패키지는 *SDK 제약 조건*을 사용하여 해당 버전을 지정할 수 있습니다. 
이 제약 조건은 pubspec의 별도 최상위 `environment` 필드 내부에 들어가고, 
종속성과 동일한 [버전 제약 조건](/tools/pub/dependencies#version-constraints) 구문을 사용합니다.

:::version-note
패키지가 2.0 이후에 도입된 기능을 사용하려면, 
해당 pubspec에 최소한 해당 기능이 도입된 버전 이상의 하위 제약 조건이 있어야 합니다. 
자세한 내용은 [언어 버전 관리][Language versioning]를 확인하세요.
:::

[Language versioning]: /guides/language/evolution#language-versioning

예를 들어, 다음 제약 조건은 이 패키지가 버전 3.0.0 이상의 모든 Dart SDK와 작동한다는 것을 나타냅니다.

```yaml
environment:
  sdk: ^3.0.0
```

Pub은 설치된 Dart SDK 버전과 SDK 제약 조건이 작동하는 패키지의 최신 버전을 찾으려고 합니다.

SDK 제약 조건을 생략하면 오류가 발생합니다. 
pubspec에 SDK 제약 조건이 없는 경우, `dart pub get`은 다음과 같은 메시지와 함께 실패합니다.

```plaintext
pubspec.yaml has no lower-bound SDK constraint.
You should edit pubspec.yaml to contain an SDK constraint:

environment:
  sdk: '^3.2.0'
  
See https://dart.dev/go/sdk-constraint
```

:::version-note
Dart 2.19 이전에는 SDK 제약에서 pub이 캐럿 구문을 허용하지 않았습니다. 
이전 버전에서는 `'>=2.12.0 <3.0.0'`과 같이 전체 범위를 제공했습니다. 
자세한 내용은 [캐럿 구문](/tools/pub/dependencies#caret-syntax) 문서를 확인하세요.
:::


#### Flutter SDK 제약 {:#flutter-sdk-constraints}

Pub은 `environment:` 필드에서 Flutter SDK 제약 조건을 지정하는 것을 지원합니다.

```yaml
environment:
  sdk: ^3.2.0
  flutter: '>= 3.22.0'
```

Flutter SDK 제약 조건은 pub가 `flutter` executable의 컨텍스트에서 실행되고, 
Flutter SDK의 `version` 파일이 버전 제약 조건의 하한을 충족하는 경우에만 충족됩니다. 
그렇지 않으면, 패키지가 선택되지 않습니다.

:::note
Flutter SDK는 Flutter 제약 조건의 하한값만 적용합니다. 
자세한 내용은 `flutter/flutter` 저장소의 [문제 #95472](https://github.com/flutter/flutter/issues/95472)를 확인하세요.
:::

Flutter SDK 제약 조건이 있는 패키지를 게시하려면, 
최소 1.19.0 이상의 Dart SDK 제약 조건을 지정해야 합니다. 
이렇게 하면 이전 버전의 pub가 실수로 Flutter가 필요한 패키지를 설치하지 않도록 할 수 있습니다.

[pubsite]: {{site.pub}}
