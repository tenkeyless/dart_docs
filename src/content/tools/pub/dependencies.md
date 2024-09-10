---
# title: Package dependencies
title: 패키지 종속성
# description: Add other packages to your app. Specify package locations, version constraints, and more.
description: 앱에 다른 패키지를 추가합니다. 패키지 위치, 버전 제약 조건 등을 지정합니다.
---

종속성은 [pub 패키지 관리자][pub package manager]의 핵심 개념 중 하나입니다. 
_종속성_ 은 패키지가 작동해야 하는 또다른 패키지입니다. 
종속성은 [pubspec](/tools/pub/pubspec)에 지정됩니다. 
패키지에서 직접 사용하는 소프트웨어인 _즉각적인 종속성_ 만 나열합니다. 
Pub은 [전이적 종속성](/tools/pub/glossary#transitive-dependency)을 처리합니다.

이 페이지에는 종속성을 지정하는 방법에 대한 자세한 정보가 있습니다. 
마지막에는 [패키지 종속성에 대한 모범 사례](#best-practices) 리스트가 있습니다.

## 개요 {:#overview}

각 종속성에 대해, 종속된 패키지의 _이름_ 과 허용하는 해당 패키지의 _버전 범위_ 를 지정합니다. 
[_소스_][_source_]도 지정할 수 있습니다. 
소스는 pub에 패키지를 찾는 방법을 알려줍니다.

[_source_]: /tools/pub/glossary#source

예를 들어, 다음 형식으로 종속성을 지정합니다.

```yaml
dependencies:
  transmogrify: ^1.0.0
```

이 YAML 코드는 기본 패키지 저장소([pub.dev]({{site.pub}}))를 사용하여, 
`transmogrify` 패키지에 대한 종속성을 생성하고, 
`1.0.0`에서 `2.0.0`까지의 모든 버전을 허용합니다. (`2.0.0`은 포함하지 않음) 
이 구문에 대해 알아보려면, [버전 제약 조건](#version-constraints)을 확인하세요.

pub.dev가 아닌 다른 소스를 지정하려면, 
`sdk`, `hosted`, `git` 또는 `path`를 사용합니다. 
예를 들어, 다음 YAML 코드는 `path`를 사용하여, 
pub에 로컬 디렉토리에서 `transmogrify`를 가져오라고 지시합니다.

```yaml
dependencies:
  transmogrify:
    path: /Users/me/transmogrify
```

다음 섹션에서는 각 종속성 소스에 대한 형식을 설명합니다.

## 종속성 소스 {:#dependency-sources}

Pub은 다음 소스를 사용하여 패키지를 찾을 수 있습니다.

* [SDK](#sdk)
* [호스팅된 패키지](#hosted-packages)
* [Git 패키지](#git-packages)
* [경로 패키지](#path-packages)

### 호스팅된 패키지 {:#hosted-packages}

_호스팅된_ 패키지는 pub.dev 사이트(또는 동일한 API를 사용하는 다른 HTTP 서버)에서 다운로드할 수 있는 패키지입니다. 
다음은 호스팅 패키지에 대한 종속성을 선언하는 예입니다.

```yaml
dependencies:
  transmogrify: ^1.4.0
```

이 예에서는 패키지가 `transmogrify`라는 호스팅 패키지에 종속되고, 
1.4.0에서 2.0.0까지의 모든 버전(2.0.0 자체는 아님)과 작동한다는 것을 지정합니다.

[자체 패키지 저장소][own package repository]를 사용하려면, 
`hosted`를 사용하여 URL을 지정할 수 있습니다. 
다음 YAML 코드는 `hosted` 소스를 사용하여, 
`transmogrify` 패키지에 대한 종속성을 만듭니다.

[own package repository]: /tools/pub/custom-package-repositories

```yaml
environment:
  sdk: '^[!2.19.0!]'

dependencies:
  transmogrify:
    [!hosted: https://some-package-server.com!]
    version: ^1.4.0
```

버전 제약은 선택 사항이지만 권장됩니다. 
버전 제약이 주어지지 않으면 `any`가 가정됩니다.

:::version-note
패키지에 [SDK 제약 조건][SDK version]이 2.19 이전인 경우, 
SDK 버전에 대한 하위 및 상위 경계 형식을 사용해야 합니다. 
해당 버전의 SDK 제약 조건 검증기는 캐럿 구문(caret syntax)을 지원하지 않습니다.

```yaml
environment:
  sdk: [!'>=2.14.0 < 3.0.0'!]
```

패키지의 [SDK 버전][SDK version]이 2.15 이전인 경우, 
더 자세한 `hosted` 형식을 사용해야 합니다.

```yaml
environment:
  sdk: [!'>=2.14.0 < 3.0.0'!]

dependencies:
  transmogrify:
    [!hosted:!]
      [!name: transmogrify!]
      [!url: https://some-package-server.com!]
    version: ^1.4.0
```
:::

[SDK version]: /guides/language/evolution#language-versioning

### Git 패키지 {:#git-packages}

때로는 최첨단 기술을 사용하고 아직 공식적으로 출시되지 않은 패키지를 사용해야 할 수도 있습니다. 
아마도 패키지 자체가 아직 개발 중이고, 동시에 개발 중인 다른 패키지를 사용하고 있을 수도 있습니다. 
이를 더 쉽게 하기 위해, [Git][] 저장소에 저장된 패키지에 직접 의존할 수 있습니다.

[git]: https://git-scm.com/

```yaml
dependencies:
  kittens:
    git: https://github.com/munificent/kittens.git
```

여기서 `git`은 이 패키지가 Git을 사용하여 발견되었음을 나타내며, 
그 뒤의 URL은 패키지를 복제하는 데 사용할 수 있는 Git URL입니다.

패키지 저장소가 비공개이더라도 [SSH를 사용하여 저장소에 연결][GitHub SSH]할 수 있다면, 
저장소의 SSH URL을 사용하여 패키지에 의존할 수 있습니다.

```yaml
dependencies:
  kittens:
    git: git@github.com:munificent/kittens.git
```

특정 커밋, 브랜치 또는 태그에 의존하려면, 설명에 `ref` 키를 추가하세요.

```yaml
dependencies:
  kittens:
    git:
      url: git@github.com:munificent/kittens.git
      ref: some-branch
```

ref는 Git에서 [커밋을 식별하는 데][commit] 허용하는 모든 것이 될 수 있습니다.

[commit]: https://www.kernel.org/pub/software/scm/git/docs/user-manual.html#naming-commits

Pub은 패키지가 Git 저장소의 루트에 있다고 가정합니다. 
저장소에서 다른 위치를 지정하려면, 저장소 루트에 상대적인 `path`를 지정합니다.

```yaml
dependencies:
  kittens:
    git:
      url: git@github.com:munificent/cats.git
      path: path/to/kittens
```

path는 Git 저장소의 루트에 상대적입니다.

Git 종속성은 [pub.dev][pubsite]에 업로드된 패키지의 종속성으로 허용되지 않습니다.

### 경로 {:#path-packages}

때로는 여러 관련 패키지를 동시에 작업하는 경우가 있습니다. 
아마도 앱을 빌드하는 동안, 프레임워크를 만들고 있을 것입니다. 
그런 경우, 개발 중에 로컬 파일 시스템에서 해당 패키지의 _라이브_ 버전에 의존하고 싶을 것입니다. 
그렇게 하면, 한 패키지의 변경 사항이 해당 패키지에 의존하는 패키지에서 즉시 적용됩니다.

이를 처리하기 위해 pub는 _경로 종속성(path dependencies)_ 을 지원합니다.

```yaml
dependencies:
  transmogrify:
    path: /Users/me/transmogrify
```

이것은 `transmogrify`의 루트 디렉토리가 `/Users/me/transmogrify`임을 나타냅니다. 
이 종속성의 경우, pub는 참조된 패키지 디렉토리의 `lib` 디렉토리에 직접 심볼릭 링크를 생성합니다. 
종속 패키지에 대한 모든 변경 사항은 즉시 표시됩니다. 
종속 패키지를 변경할 때마다 pub를 실행할 필요는 없습니다.

상대 경로가 허용되며, pubspec이 포함된 디렉토리에 대한 상대 경로로 간주됩니다.

경로 종속성은 로컬 개발에 유용하지만, 외부 세계와 코드를 공유할 때는 작동하지 않습니다. 
모든 사람이 파일 시스템에 접근할 수 있는 것은 아닙니다. 
이 때문에 pubspec에 경로 종속성이 있는 경우, 
[pub.dev 사이트][pubsite]에 패키지를 업로드할 수 없습니다.

대신, 일반적인 워크플로는 다음과 같습니다.

1. 로컬에서 pubspec을 편집하여 경로 종속성을 사용합니다.
2. 기본 패키지와 종속된 패키지에서 작업합니다.
3. 둘 다 작동하면, 종속 패키지를 게시합니다.
4. pubspec을 변경하여, 종속 패키지의 현재 호스팅된 버전을 가리키도록 합니다.
5. 원한다면 메인 패키지도 게시합니다.

### SDK {:#sdk}

SDK 소스는 패키지와 함께 제공되는 모든 SDK에 사용되며, 패키지 자체가 종속성일 수 있습니다. 
현재 Flutter가 지원되는 유일한 SDK입니다.

구문은 다음과 같습니다.

```yaml
dependencies:
  flutter_driver:
    sdk: flutter
```

`sdk:` 뒤의 식별자는 패키지가 어느 SDK에서 왔는지 나타냅니다. 
`flutter`인 경우, 종속성은 다음과 같은 경우 충족 가능합니다.

* Pub이 `flutter` executable의 컨텍스트에서 실행 중
* Flutter SDK에 지정된 이름의 패키지가 포함되어 있음

알 수 없는 식별자인 경우, 종속성은 항상 충족되지 않은 것으로 간주됩니다.

## 버전 제약 {:#version-constraints}

패키지 A가 패키지 B에 종속되어 있다고 가정해 보겠습니다. 
패키지 A의 주어진 버전과 호환되는 패키지 B의 버전을 다른 개발자에게 어떻게 전달할 수 있을까요?

개발자에게 버전 호환성을 알리려면 버전 제약 조건을 지정하세요. 
패키지 사용자에게 유연성을 제공하기 위해 가능한 한 광범위한 버전을 허용해야 합니다. 
이 범위에는 작동하지 않거나 테스트되지 않은 버전이 제외되어야 합니다.

Dart 커뮤니티는 시맨틱 버전 관리<sup id="fnref:semver"><a href="#fn:semver">1</a></sup>를 사용합니다.

Dart 2.19부터 _기존 구문(traditional syntax)_ 또는 _캐럿 구문(caret syntax)_ 을 사용하여, 
버전 제약 조건을 표현할 수 있습니다. 
두 구문 모두 호환되는 버전 범위를 지정합니다.

기존 구문은 `'>=1.2.3 <2.0.0'`과 같이 명시적인 범위를 제공합니다. 
캐럿 구문은 명시적인 시작 버전 `^1.2.3`을 제공합니다.

```yaml
environment:
  # 이 패키지는 3.2부터 시작하는 Dart SDK 3.x 버전을 사용해야 합니다.
  sdk: ^3.2.0

dependencies:
  transmogrify:
    hosted:
      name: transmogrify
      url: https://some-package-server.com
    # 이 패키지는 1.4부터 시작하는 transmogrify 1.x 버전을 사용해야 합니다.
    version: ^1.4.0
```

pub의 버전 시스템에 대해 자세히 알아보려면, [패키지 버전 관리 페이지][package versioning page]를 참조하세요.

[package versioning page]: /tools/pub/versioning#semantic-versions

### 기존 구문 {:#traditional-syntax}

기존 구문을 사용하는 버전 제약 조건은 다음 값 중 하나를 사용할 수 있습니다.

| **값** |                **허용**               | **Use?** |                                                                        **메모**                                                                        |
|:---------:|:----------------------------------------|:--------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------|
|   `any`   | 모든 버전                            |    No    | 비어 있는 버전 제약 조건을 명시적으로 선언하는 역할을 합니다.                                                                                           |
|  `1.2.3`  | 주어진 버전만                  |    No    | 패키지를 사용하는 앱에 추가적인 제한을 가하기 때문에, 패키지 채택이 제한됩니다.                                                     |
| `>=1.2.3` | 주어진 버전 또는 이후 버전                  |    Yes   |                                                                                                                                                         |
|  `>1.2.3` | 주어진 버전보다 이후 버전   |    No    |                                                                                                                                                         |
| `<=1.2.3` | 주어진 버전 또는 이전 버전                |    No    |                                                                                                                                                         |
|  `<1.2.3` | 주어진 버전보다 이전 버전 |    No    | 패키지와 *호환되지 않는* 상위 버전을 알고 있을 때, 이것을 사용하세요. 이 버전은 breaking change가 도입되는 첫 번째 버전일 수 있습니다. |

{:.table}

버전 값의 범위가 교차할 때 버전 값의 조합을 지정할 수 있습니다. 
예를 들어 버전 값을 `'>=1.2.3 <2.0.0'`으로 설정하면, 
두 가지 제한이 결합되어 종속성은 `2.0.0` 자체를 제외한, 
`1.2.3`에서 `2.0.0`까지의 모든 버전이 될 수 있습니다.

:::warning
버전 제약 조건에 보다 큼(**>**) 문자를 포함하는 경우, **전체 제약 조건 문자열을 인용**합니다. 
이렇게 하면 YAML이 해당 문자를 YAML 구문으로 해석하지 못합니다. 
예를 들어, `>=1.2.3 <2.0.0`은 절대 사용하지 마세요. 
`'>=1.2.3 <2.0.0'` 또는 `^1.2.3`을 사용하세요.
:::

### 캐럿 구문 {:#caret-syntax}

캐럿 구문은 버전 제약 조건을 간결하게 표현합니다. 
`^version`은 _주어진 버전과 하위 호환성이 보장되는 모든 버전의 범위_ 를 의미합니다. 
이 범위에는 다음 버전까지의 모든 버전이 포함되어 breaking change를 도입합니다. 
Dart는 시맨틱 버전 관리를 사용하므로, 
이는 모든 패키지 버전 1.0 이상의 다음 major 버전이 되고, 
모든 패키지 버전 1.0 이전의 다음 minor 버전이 됩니다.

| 버전 값 | 커버하는 범위 | 캐럿 구문 | 기존 구문  |
|:-------------:|:---------------:|:------------:|:-------------------:|
| >=1.0         | 다음 major      | `^1.3.0`     | `'>=1.3.0 <2.0.0'`  |
| <1.0          | 다음 minor      | `^0.1.2`     | `'>=0.1.2 <0.2.0'`  |

{:.table}

다음 예에서는 캐럿 구문을 보여줍니다.

```yaml
dependencies:
  # 2.0.0을 제외한 1.3.0부터 1.y.z까지의 모든 버전을 포함합니다.
  path: ^1.3.0
  # 2.0.0을 제외한 1.1.0부터 1.y.z까지의 모든 버전을 포함합니다.
  collection: ^1.1.0
  # 0.2.0을 제외한 0.1.2부터 0.1.z까지의 모든 버전을 포함합니다.
  string_scanner: ^0.1.2
```

## 개발 종속성 {:#dev-dependencies}

Pub은 두 가지 종속성 플레이버를 지원합니다. 일반 종속성과 _dev 종속성_ 입니다. 
dev 종속성은 _사용자가 종속된 패키지의 dev 종속성이 무시_ 된다는 점에서 일반 종속성과 다릅니다.
다음은 예입니다.

`transmogrify` 패키지가 테스트에서 `test` 패키지를 사용하고 테스트에서만 사용한다고 가정해 보겠습니다. 
누군가가 `transmogrify`만 사용하고 싶어한다면(라이브러리를 가져오기 위해) 실제로 `test`가 필요하지 않습니다. 
이 경우 `test`를 dev 종속성으로 지정합니다. 
pubspec은 다음과 같습니다.

```yaml
dev_dependencies:
  test: ^1.25.0
```

Pub은 패키지가 종속된 모든 패키지와 _해당_ 패키지가 종속된 모든 것을 전이적으로 가져옵니다. 
또한 패키지의 개발 종속성을 가져오지만, 종속된 모든 패키지의 개발 종속성은 _무시_ 합니다. 
Pub은 _당신_ 의 패키지의 개발 종속성만 가져옵니다. 
따라서 패키지가 `transmogrify`에 종속되면, `transmogrify`는 가져오지만, `test`는 가져오지 않습니다.

일반 종속성과 개발 종속성 중 하나를 선택하는 규칙은 간단합니다. 
종속성이 `lib` 또는 `bin` 디렉터리의 어떤 것에서 가져온 경우, 일반 종속성이어야 합니다. 
`test`, `example` 등에서만 가져온 경우, 개발 종속성이어야 하며 그래야 합니다.

개발 종속성을 사용하면 종속성 그래프가 더 작아집니다. 
그러면 `pub`이 더 빨리 실행되고, 모든 제약 조건을 충족하는 패키지 버전 세트를 더 쉽게 찾을 수 있습니다.

## 종속성 재정의 {:#dependency-overrides}

`dependency_overrides`를 사용하면, 종속성에 대한 모든 참조를 일시적으로 재정의할 수 있습니다.

예를 들어, 게시된 패키지인 transmogrify의 로컬 사본을 업데이트하고 있을 수 있습니다. 
Transmogrify는 종속성 그래프의 다른 패키지에서 사용되지만, 
각 패키지를 로컬로 복제하고 각 pubspec을 변경하여 transmogrify의 로컬 사본을 테스트하고 싶지는 않습니다.

이 상황에서 `dependency_overrides`를 사용하여 종속성을 재정의하여, 
패키지의 로컬 사본을 보관하는 디렉터리를 지정할 수 있습니다.

pubspec은 다음과 같습니다.

```yaml
name: my_app
dependencies:
  transmogrify: ^1.2.0
dependency_overrides:
  transmogrify:
    path: ../transmogrify_patch/
```

[`dart pub get`][] 또는 [`dart pub upgrade`][]를 실행하면, 
pubspec의 lockfile이 업데이트되어 종속성에 대한 새 경로가 반영되고, 
transmogrify가 사용되는 곳에서는 pub가 대신 로컬 버전을 사용합니다.

`dependency_overrides`를 사용하여 패키지의 특정 버전을 지정할 수도 있습니다.

```yaml
name: my_app
dependencies:
  transmogrify: ^1.2.0
dependency_overrides:
  transmogrify: '3.2.1'
```

:::warning
종속성 재정의를 사용하는 데는 어느 정도 위험이 따릅니다. 
예를 들어, 패키지가 지원한다고 주장하는 범위를 벗어난 버전을 지정하기 위해 재정의를 사용하거나, 
예상치 못한 동작이 있는 패키지의 로컬 복사본을 지정하기 위해 재정의를 사용하면, 
애플리케이션이 중단될 수 있습니다.
:::

패키지 resolution 중에 **패키지의 자체 pubspec**에 있는 종속성 재정의만 고려됩니다. 
종속된 패키지 내부의 종속성 재정의는 무시됩니다.

결과적으로, pub.dev에 패키지를 게시하는 경우, 
패키지의 종속성 재정의는 패키지의 모든 사용자가 무시한다는 점을 명심하세요.

## 모범 사례 {:#best-practices}

종속성을 관리하는 데 적극적으로 대처하세요. 
가능하면 패키지가 최신 버전의 패키지에 종속되도록 하세요. 
패키지가 오래된 패키지에 종속된 경우, 
해당 오래된 패키지는 종속성 트리에 있는 다른 오래된 패키지에 종속될 수 있습니다. 
오래된 버전의 패키지는 앱의 안정성, 성능 및 품질에 부정적인 영향을 미칠 수 있습니다.

패키지 종속성에 대한 다음 모범 사례를 권장합니다.

### 캐럿 구문을 사용하세요 {:#use-caret-syntax}

[caret 구문](#caret-syntax)을 사용하여 종속성을 지정합니다. 
이렇게 하면, pub 도구가 패키지의 새 버전을 사용할 수 있을 때 선택할 수 있습니다. 
또한, 허용되는 버전에 상한을 설정합니다.

### 최신 stable 패키지 버전을 사용하세요 {:#depend-on-the-latest-stable-package-versions}

[`dart pub upgrade`][]를 사용하여, pubspec에서 허용하는 최신 패키지 버전으로 업데이트합니다. 
최신 stable 버전에 없는 앱이나 패키지의 종속성을 식별하려면, [`dart pub outdated`][]를 사용합니다.

### 개발 종속성에 대한 버전 제약을 강화합니다 {:#tighten-version-constraints-for-dev-dependencies}

개발 종속성은 개발 시에만 필요한 패키지를 정의합니다. 
완성된 앱에는 이러한 패키지가 필요하지 않습니다. 
이러한 패키지의 예로는 테스트 또는 코드 생성 도구가 있습니다. 
패키지가 종속된 최신 버전의 하한을 갖도록, 
[`dev_dependencies`][dev-dep]에서 패키지의 버전 제약 조건을 설정합니다.

개발 종속성의 버전 제약 조건을 강화하면, 다음과 유사할 수 있습니다.

```yaml
dev_dependencies:
  build_runner: ^2.4.10
  lints: ^2.1.1
  test: ^1.25.8
```

이 YAML은 `dev_dependencies`를 최신 패치 버전으로 설정합니다.

[dev-dep]: /tools/pub/dependencies#dev-dependencies

### 패키지 종속성을 업데이트할 때마다 테스트하세요 {:#test-whenever-you-update-package-dependencies}

pubspec을 업데이트하지 않고 [`dart pub upgrade`][]를 실행하면, 
API는 동일하게 유지되고 코드는 이전과 동일하게 실행됩니다. 
하지만 테스트를 통해 확인하세요. 
pubspec을 수정하고 새로운 주요 버전으로 업데이트하면, 
breaking changes이 발생할 수 있으므로 더욱 철저하게 테스트해야 합니다.

### 다운그레이드된 종속성으로 테스트 {:#test-with-downgraded-dependencies}

게시용 패키지를 개발할 때는, 
가능한 한 가장 광범위한 종속성 제약 조건을 허용하는 것이 좋습니다. 
광범위한 종속성 제약 조건은 패키지 소비자가 버전 해결 충돌에 직면할 가능성을 줄여줍니다.

예를 들어, `foo: ^1.2.3`에 대한 종속성이 있고, `foo`의 버전 `1.3.0`이 릴리스된 경우, 
기존 종속성 제약 조건(`^1.2.3`)을 유지하는 것이 합리적일 수 있습니다. 
하지만, 패키지가 `1.3.0`에서 추가된 기능을 사용하기 시작하면, 제약 조건을 `^1.3.0`으로 높여야 합니다.

하지만, 필요할 때 종속성 제약 조건을 높이는 것을 잊기 쉽습니다. 
따라서, 게시하기 전에 다운그레이드된 종속성에 대해 패키지를 테스트하는 것이 가장 좋습니다.

다운그레이드된 종속성에 대해 테스트하려면, 
[`dart pub downgrade`][]를 실행하고, 
패키지가 오류 없이 여전히 분석되고 모든 테스트를 통과하는지 확인합니다.

```console
dart pub downgrade
dart analyze
dart test
```

다운그레이드된 종속성을 사용한 테스트는 최신 종속성을 사용한 일반 테스트와 함께 수행해야 합니다. 
종속성 제약 조건을 높여야 하는 경우, 
직접 변경하거나 `dart pub upgrade --tighten`을 사용하여, 
종속성을 최신 버전으로 업데이트합니다.

:::note
`dart pub downgrade`로 테스트하면, 
그렇지 않으면 발견하지 못했을 비호환성을 찾을 수 있습니다. 
하지만 비호환성의 가능성을 배제하지는 않습니다.

종종 버전 조합이 너무 다양해서 모든 버전을 테스트하는 것은 불가능합니다. 
종속성 제약 조건으로 허용되는 이전 버전도, 
패키지 자체 또는 `dev_dependencies`의 상호 호환되지 않는 버전 제약 조건으로 인해, 
해결할 수 없는 경우가 있습니다.
:::

[`dart pub downgrade`]: /tools/pub/cmd/pub-downgrade

### 다운로드한 패키지의 무결성을 확인하세요 {:#verify-the-integrity-of-downloaded-packages}

새로운 종속성을 검색할 때, 
[`--enforce-lockfile`][enforce-lock] 옵션을 사용하여 
추출된 패키지 내용이 원래 아카이브의 내용과 일치하는지 확인합니다. 
[lockfile][]을 수정하지 않고, 
이 플래그는 다음과 같은 경우에만 새로운 종속성을 해결합니다.

* `pubspec.yaml`이 충족됨
* `pubspec.lock`이 누락되지 않음
* 패키지의 [컨텐츠 해시][content hashes]가 일치함

[enforce-lock]: /tools/pub/cmd/pub-get#enforce-lockfile
[lockfile]: /tools/pub/glossary#lockfile
[content hashes]: /tools/pub/glossary#content-hashes
---

<aside id="fn:semver" class="footnote">

[1] Pub은 [시맨틱 버전 사양][semantic versioning specification]의 버전 `2.0.0-rc.1`을 따릅니다. 이 버전에서는 패키지가 빌드 식별자(`+12345`)를 사용하여 버전을 구분할 수 있기 때문입니다. <a href="#fnref:semver">↩</a>
</aside>

[GitHub SSH]: https://help.github.com/articles/connecting-to-github-with-ssh/
[pub package manager]: /guides/packages
[`dart pub get`]: /tools/pub/cmd/pub-get
[`dart pub outdated`]: /tools/pub/cmd/pub-outdated
[`dart pub upgrade`]: /tools/pub/cmd/pub-upgrade
[pubsite]: {{site.pub}}
[semantic versioning specification]: https://semver.org/spec/v2.0.0-rc.1.html
