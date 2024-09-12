---
title: build_runner
description: Dart 코드를 빌드, 테스트, 실행하기 위한 도구입니다.
---

[build_runner][] 패키지는 생성된 파일을 테스트하거나, 
소스 및 생성된 파일을 모두 제공하는 것을 포함하여, 
파일을 생성하기 위한 범용 명령을 제공합니다. 
이 페이지에서는 `build_runner`를 사용하는 방법을 설명합니다. 
특정 패키지와 함께 build_runner를 사용하는 방법을 알아보려면, 
해당 패키지의 문서를 참조하세요.

:::note
웹 개발자라면, [`webdev` 도구][webdev]를 사용하여 웹앱을 빌드하고 제공하세요.
:::

build_runner 명령은 [Dart 빌드 시스템][build]를 사용하여, 
입력 파일에서 출력 파일을 생성하는 패키지인 _builders_ 와 함께 작동합니다. 
예를 들어, [json_serializable][] 및 [built_value_generator][] 패키지는 
Dart 코드를 생성하는 빌더를 정의합니다.

Dart 빌드 시스템은 성능 문제가 있는 reflection과 
Dart 컴파일러가 지원하지 않는 매크로에 대한 좋은 대안이지만, 
Dart 코드를 읽고 쓰는 것 이상을 할 수 있습니다. 
예를 들어, [sass_builder][] 패키지는, 
`.scss` 및 `.sass` 파일에서 `.css` 파일을 생성하는 빌더를 구현합니다.

## build_runner 설정하기 {:#setting-up-build_runner}

build_runner를 사용하려면, 
앱의 pubspec에 **build_runner**에 대한 [dev 종속성][dev dependency]을 추가하세요.

<?code-excerpt "build_runner_usage/pubspec.yaml" from="dev_dependencies" to="build_test" replace="/args.*/# ···/g"?>
```yaml
dev_dependencies:
  # ···
  build_runner: ^2.4.10
  build_test: ^2.2.2
```

**build_test**에 의존하는 것은 선택 사항입니다. 코드를 테스트할 경우 사용하세요.

평소처럼 `pubspec.yaml`이 변경된 후에, `dart pub get` 또는 `dart pub upgrade`를 실행합니다.

```console
$ dart pub get
```

## 빌트인 명령 사용 {:#using-built-in-commands}

다음은 build_runner **build** 명령을 사용하는 예입니다.

```console
$ # From a directory that contains a pubspec.yaml file:
$ dart run build_runner build
```

build_runner 패키지에는 다음 명령이 포함되어 있습니다.

build
: 일회성 빌드를 수행합니다.

serve
: 개발 서버를 실행합니다. 
  이 명령을 직접 사용하는 대신, 
  편리한 기본 동작이 있는 [`webdev serve`][webdev serve]를 사용할 수 있습니다.

test
: [테스트][tests]를 실행합니다.

watch
: 입력 파일에 대한 편집을 감시하는 빌드 서버를 시작합니다. 
  증분적 재구축(incremental rebuilds)을 수행하여 변경 사항에 응답합니다.

## 더 많은 정보 {:#more-information}

웹 관련 코드로 작업하는 경우, [webdev 페이지][webdev]를 참조하세요.

build_runner 사용에 대한 자세한 내용은 다음을 참조하세요.

- build_runner를 사용해야 하는 패키지에 대한 문서. 
  이러한 패키지는 일반적으로 [build][on build] 또는 [build_runner][on build_runner.]에 대한 종속성을 갖습니다.
- Build_runner 문서:
  - [build_runner 시작하기][Getting started with build_runner]
  - [빌드 FAQ][Build FAQ]

[build]: {{site.repo.dart.org}}/build
[Build FAQ]: {{site.repo.dart.org}}/build/blob/master/docs/faq.md
[build_runner]: {{site.pub-pkg}}/build_runner
[built_value_generator]: {{site.pub-pkg}}/built_value_generator
[dev dependency]: /tools/pub/dependencies#dev-dependencies
[Getting started with build_runner]: {{site.repo.dart.org}}/build/blob/master/docs/getting_started.md
[json_serializable]: {{site.pub-pkg}}/json_serializable
[on build]: {{site.pub-pkg}}?q=dependency%3Abuild
[on build_runner.]: {{site.pub-pkg}}?q=dependency%3Abuild_runner
[sass_builder]: {{site.pub-pkg}}/sass_builder
[tests]: /guides/testing
[webdev]: /tools/webdev
[webdev serve]: /tools/webdev#serve
