---
title: dart pub add
# description: Use dart pub add to add a dependency.
description: dart pub add를 사용하여 종속성을 추가합니다.
---

_Add_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub add [{dev|override}:]<package>[:descriptor] [[{dev|override}:]<package>[:descriptor] ...] [options]
```

이 명령은 지정된 패키지를 종속성으로 `pubspec.yaml`에 추가한 다음, 
종속성을 검색하여 `pubspec.yaml`을 해결합니다.

다음 예제 명령은 `pubspec.yaml`을 편집하여 `http` 패키지를 추가한 다음, 
`dart pub get`을 호출하는 것과 같습니다.

```console
$ dart pub add http
```

## 버전 제약 {:#version-constraint}

기본적으로, `dart pub add`는 SDK 제약 조건 및 종속성과 호환되는 [pub.dev 사이트]({{site.pub}})에서, 
최신 stable 버전의 패키지를 사용합니다. 
예를 들어 `0.13.3`이 `http` 패키지의 최신 stable 버전인 경우, 
`dart pub add http`는 `pubspec.yaml`의 `dependencies`에 `http: ^0.13.3`을 추가합니다.

제약 조건 또는 제약 조건 범위를 지정할 수도 있습니다.

```console
$ dart pub add foo:2.0.0
$ dart pub add foo:'^2.0.0'
$ dart pub add foo:'>=2.0.0 <3.0.1'
```

지정된 패키지가 `pubspec.yaml`에 있는 기존 종속성인 경우, 
`dart pub add`는 해당 종속성의 제약 조건을 명령에서 지정된 종속성으로 업데이트합니다.

## 개발 종속성 {:#dev-dependency}

`dev:` 접두사는 패키지를 일반 종속성이 아닌 [dev 종속성][dev dependency]으로 추가합니다.

[dev dependency]: /tools/pub/dependencies#dev-dependencies

```console
$ dart pub add dev:foo           # foo의 최신 호환 stable 버전을 추가합니다.
$ dart pub add dev:foo:^2.0.0    # foo의 지정된 제약 조건을 추가합니다.
$ dart pub add foo dev:bar       # 일반 종속성 foo와 dev 종속성 bar를 동시에 추가합니다.
```

_이전에는 `-d, --dev` 옵션이었습니다_:

```console
$ dart pub add --dev foo
```

## 종속성 재정의 {:#dependency-override}

[종속성 재정의][dependency override]를 지정하려면, 
`override:` 접두사를 추가하고 [버전 제약 조건](#version-constraint) 또는 [소스 설명자](#source-descriptor)를 포함합니다.

[dependency override]: /tools/pub/dependencies#dependency-overrides

**예:** `package:foo`에 대한 모든 참조를 재정의하여, 
  패키지의 `1.0.0` 버전을 사용하려면, 다음 명령을 실행합니다.

```console
$ dart pub add override:foo:1.0.0
```

이렇게 하면 `pubspec.yaml` 파일에 오버라이드가 추가됩니다.

```yaml
dependency_overrides:
  foo: 1.0.0
```

## 소스 설명자 {:#source-descriptor}

:::version-note
YAML 형식의 설명자 구문은 Dart 2.19에 추가되었습니다. 
설명자는 `--path`, `--sdk`, `--git-<option>` 등의 인수를 대체합니다. 
Pub은 여전히 ​​이러한 인수를 지원하지만 권장되는 방법은 이제 YAML 설명자입니다. 
설명자와 대체된 인수는 함께 사용할 수 없습니다.
:::

YAML 설명자 구문을 사용하면 다양한 소스에서 여러 패키지를 추가하고, 
각각에 다른 옵션과 제약 조건을 적용할 수 있습니다.

```plaintext
$ dart pub add [options] [{dev|override}:]<package>[:descriptor] [[{dev|override}:]<package>[:descriptor] ...]
```

구문은 `pubspec.yaml`에 종속성이 작성되는 방식을 반영합니다.

```plaintext
'<package>:{"<source>":"<descriptor>"[,"<source>":"<descriptor>"],"version":"<constraint>"}'
```

### `git`

[git 종속성](/tools/pub/dependencies#git-packages)을 추가합니다.

```console
$ dart pub add 'foo:{"git":"https://github.com/foo/foo"}'
```

저장소와 해당 저장소 내의 브랜치 또는 커밋, 또는 정확한 위치를 지정할 수 있습니다.

```console
$ dart pub add 'foo:{"git":{"url":"../foo.git","ref":"branch","path":"subdir"}}'
```

#### `url`

지정된 Git 저장소의 패키지에 따라 달라집니다.

_이전에는 `--git-url=<git_repo_url>` 옵션_:

```console
$ dart pub add http --git-url=https://github.com/my/http.git
```

#### `ref`

`url`을 사용하면, Git 저장소의 지정된 브랜치 또는 커밋에 따라 달라집니다.

_이전에는 `--git-ref=<branch_or_commit>` 옵션_:

```console
$ dart pub add http --git-url=https://github.com/my/http.git --git-ref=tmpfixes
```

#### `path`

`url`을 사용하면, Git 저장소 내의 패키지 위치를 지정합니다.

_이전에는 `--git-path=<directory_path>` 옵션이었습니다._

### `hosted`

지정된 URL의 패키지 서버에 종속된 [호스트된 종속성][hosted dependency]을 추가합니다.

```console
$ dart pub add 'foo:{"hosted":"my-pub.dev"}'
```

_이전에는 `--hosted-url=<package_server_url>` 옵션이었습니다_.

[hosted dependency]: /tools/pub/dependencies#hosted-packages

### `path`

로컬에 저장된 패키지에 [경로 종속성][path dependency]을 추가합니다.

```console
$ dart pub add 'foo:{"path":"../foo"}'
```

_이전에는 `--path=<directory_path>` 옵션이었습니다_.

[path dependency]: /tools/pub/dependencies#path-packages

### `sdk`

지정된 SDK 소스에서 패키지를 추가합니다.

```console
$ dart pub add 'foo:{"sdk":"flutter"}'
```

_이전에는 `--sdk=<sdk_name>` 옵션_:

```console
$ dart pub add foo --sdk=flutter
```

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

:::note
이전 `pub add` 구문은 옵션(YAML 설명자 없이)에 대해, 
명령 호출에 포함된 모든 패키지에 지정된 옵션을 적용합니다. 
예를 들어, `dart pub add test http --dev`는
`test`와 `http` 패키지를 모두 dev 종속성으로 추가합니다.
:::

### `--[no-]offline`

{% render 'tools/pub-option-no-offline.md' %}

### `-n, --dry-run`

어떤 종속성이 변경될지 보고하지만, 아무것도 변경하지 않습니다.

### `--[no-]precompile`

기본적으로 pub는 직접 종속성에서 실행 파일을 사전 컴파일합니다. (`--precompile`)
사전 컴파일을 방지하려면, `--no-precompile`을 사용합니다.


{% render 'pub-problems.md' %}
