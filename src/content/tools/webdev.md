---
title: webdev
# description: Command-line tools for Dart web development.
description: Dart 웹 개발을 위한 명령줄 도구.
---

이 페이지에서는 `webdev`를 사용하여 앱을 컴파일하는 방법과, 
`build_runner`를 사용하여 앱을 테스트하는 방법을 설명합니다.

## 셋업 {:#setup}

`webdev`를 사용하려면 다음 지침을 따르세요.

`webdev`를 사용하기 전에, 
앱에 [build_runner][] 및 [build_web_compilers][] 패키지에 대한 종속성을 추가하세요. 
`build_runner` 패키지는 `webdev`에 스크립팅 기능을 추가합니다.

```console
$ dart pub add build_runner build_web_compilers --dev
```

### webdev 설치 및 업데이트 {:#installing-and-updating-webdev}

`dart pub`를 사용하여 [모든 사용자][all users]를 위해 `webdev`를 설치하세요.

```console
$ dart pub global activate webdev
```

동일한 명령을 사용하여 `webdev`를 업데이트합니다. 
Dart SDK를 업데이트하거나, `webdev` 명령이 설명할 수 없는 방식으로 실패할 때 `webdev`를 업데이트합니다.

[all users]: /tools/pub/cmd/pub-global


### build_* 패키지에 대한 의존성 {:#depending-on-build_-packages}

`webdev`를 사용하려면, 
**build_runner** 및 **build_web_compilers** 패키지에 종속된 패키지의 루트 디렉토리에 있어야 합니다. 
앱을 테스트하는 경우, **build_test**에도 종속되어야 합니다.

이러한 패키지에 종속하려면, 다음 [dev_dependencies][]를 앱의 `pubspec.yaml` 파일에 추가하세요.

```yaml
  dev_dependencies:
    # ···
    build_runner: ^2.4.10
    build_test: ^2.2.2
    build_web_compilers: ^4.0.10
```

평소처럼 `pubspec.yaml`이 변경된 후에, `dart pub get` 또는 `dart pub upgrade`를 실행합니다.

```console
$ dart pub get
```

## Dart 패키지의 명령을 사용하여 컴파일 및 테스트 {:#using-commands-from-dart-packages-to-compile-and-test}

이 도구는 두 가지 방법으로 컴파일할 수 있습니다. 
하나는 디버깅을 더 쉽게 만드는 방법(`serve`)이고, 
다른 하나는 작고 빠른 코드(`build`)입니다.

개발 컴파일러는 증분 업데이트를 지원하고, 
[비동기 모듈 정의(AMD) 모듈](https://github.com/amdjs/amdjs-api/blob/master/AMD.md#amd)을 생성합니다. 
[`webdev serve`](#serve)를 사용하면, Dart 파일을 편집하고, Chrome에서 새로 고침하고, 
짧은 시간 내에 편집 내용을 볼 수 있습니다. 
이 속도는 앱에 필요한 모든 패키지가 아니라, 업데이트된 모듈을 컴파일하는 데서 나옵니다.

첫 번째 컴파일은 전체 앱을 컴파일하기 때문에 가장 오래 걸립니다. 
[`serve`](#serve) 명령이 실행되는 동안, 연속 빌드는 더 빨리 컴파일됩니다.

프로덕션 컴파일러는 단일의 축소된 JavaScript 파일을 생성합니다.

이 섹션에서는 다음 명령을 사용하는 방법을 설명합니다.

[webdev serve](#serve)
: JavaScript 앱을 지속적으로 빌드하는 개발 서버를 실행합니다.

[webdev build](#build)
: JavaScript 앱의 배포 가능한 버전을 빌드합니다.

[build_runner test](#test)
: 테스트를 실행합니다.

빌드 구성 파일을 사용하여 빌드를 커스터마이즈할 수 있습니다. 
빌드 구성 파일에 대해 자세히 알아보려면, [build_web_compilers][] 패키지를 참조하세요.

### webdev serve {:#serve}

웹 앱의 개발 버전을 serve하려면, 다음 명령을 실행하세요.

```plaintext
$ webdev serve [--debug | --release] [ [<directory>[:<port>]] ... ]
```

이 명령은 앱을 serves하고, 소스 코드 변경 사항을 감시하는 개발 서버를 시작합니다. 
기본적으로, 이 명령은 [localhost:8080](localhost:8080)에서 앱을 serves합니다.

```console
$ webdev serve
```

첫 번째 `webdev serve`는 느리게 컴파일됩니다. 
첫 번째 컴파일 후, 디스크에 assets을 캐시합니다. 
이렇게 하면, 이후 빌드가 더 빨리 컴파일됩니다.

:::note
개발 컴파일러는 **Chrome만** 지원합니다. 
다른 브라우저에서 앱을 보려면, 프로덕션 컴파일러를 사용하세요. 
프로덕션 컴파일러는 최신 두 버전의 Chrome, Edge, Firefox, Safari를 지원합니다.
:::

[Dart DevTools][]를 활성화하려면, `--debug` 플래그를 추가하세요.

```console
$ webdev serve --debug  # enables Dart DevTools
```

개발 컴파일러 대신 프로덕션 컴파일러를 사용하려면, `--release` 플래그를 추가하세요.

```console
$ webdev serve --release  # uses production compiler
```

다양한 디렉토리 포트 구성을 지정할 수 있습니다.

예를 들어, 다음 명령은 테스트 포트를 기본(8081)에서 8083으로 변경합니다.

```console
$ webdev serve web test:8083 # App: 8080; tests: 8083
```

### webdev build {:#build}

다음 명령을 사용하여 앱을 빌드하세요.

```plaintext
$ webdev build [--no-release] --output [<dirname>:]<dirname>
```

기본적으로, `build` 명령은 프로덕션 JavaScript 컴파일러를 사용하여 앱의 프로덕션 버전을 만듭니다. 
개발 JavaScript 컴파일러로 컴파일하려면, `--no-release`를 추가합니다. 
`--output` 옵션을 사용하여, Dart가 최상위 프로젝트 폴더를 컴파일하고, 출력을 쓰는 위치를 제어합니다.

다음 명령은 프로젝트의 최상위 `web` 폴더를 `build` 디렉토리로 컴파일하는 방법을 보여줍니다. 
이 명령은 기본적으로 프로덕션 JavaScript 컴파일러를 사용합니다.

```console
$ webdev build --output web:build
```


### build_runner test {:#test}

`build_runner test` 명령을 사용하여 앱의 구성 요소 테스트를 실행합니다.

```console
$ dart run build_runner test [build_runner options] -- -p <platform> [test options]
```

:::tip
명령이 테스트 파일을 로드하는 데 실패하면, 
앱의 `pubspec`에 `build_test`에 대한 `dev_dependency`가 있는지 확인하세요.
:::

예를 들어, 모든 Chrome 플랫폼 테스트를 실행하는 방법은 다음과 같습니다.

```console
$ dart run build_runner test -- -p chrome
```

사용 가능한 모든 build_runner 옵션을 보려면, `--help` 또는 `-h` 옵션을 사용하세요.

```console
$ dart run build_runner test -h
```

Dart는 빈 `--` 인수 뒤에 인수를 [test package][] 러너에 직접 전달합니다. 
테스트 패키지 러너에 대한 모든 명령줄 옵션을 보려면 다음 명령을 사용합니다.

```console
$ dart test -h
```

## 더 많은 정보 {:#more-information}

`webdev` 옵션의 전체 리스트를 보려면, `webdev --help`를 실행하거나, [webdev 패키지][webdev]를 참조하세요.

다음 페이지도 참조하세요.

* [build_runner:][build_runner]
  build_runner와 빌트인 명령을 소개하고, 자세한 정보를 알려줍니다.
* [build_web_compilers:][build_web_compilers]
  빌드 구성에 대한 정보가 있으며, `dart2js_args`를 사용하여, 
  [컴파일러 옵션](/tools/dart-compile#js)을 지정하는 예가 있습니다.


[build_runner]: /tools/build_runner
[build_web_compilers]: {{site.pub-pkg}}/build_web_compilers
[Dart DevTools]: /tools/dart-devtools
[dev_dependencies]: /tools/pub/dependencies#dev-dependencies
[test package]: {{site.pub-pkg}}/test
[webdev]: {{site.pub-pkg}}/webdev
