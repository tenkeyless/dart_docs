---
title: dart doc
description: >-
   공개 Dart 라이브러리에 대한 HTML 참조 문서를 생성하는 방법을 알아보세요.
---

`dart doc` 명령은 Dart 소스 코드에 대한 HTML 참조 문서를 생성합니다.

## 문서 작성 {:#write}

생성된 문서에 참조 텍스트와 예를 추가하려면, 
[문서 주석][documentation comments]을 [Markdown][] 포맷팅과 함께 사용합니다. 
문서 주석 작성에 대한 지침은 [효과적인 Dart: 문서][Effective Dart: Documentation] 가이드를 확인하세요.

[documentation comments]: /language/comments#documentation-comments
[Markdown]: {{site.pub-pkg}}/markdown
[Effective Dart: Documentation]: /effective-dart/documentation

## API 문서 생성 {:#generate}

:::note
문서를 생성하려면, 먼저 [`dart pub get`](/tools/pub/cmd/pub-get)을 실행해야 하며, 
패키지는 오류 없이 [`dart analyst`](/tools/dart-analyze)를 통과해야 합니다.
:::

패키지에 대한 문서를 생성하려면, 패키지의 루트 디렉토리에서 `dart doc .`를 실행합니다. 
예를 들어, `my_package` 패키지에 대한 API 문서를 생성하는 것은 다음과 유사할 수 있습니다.

```console
$ cd my_package
$ dart pub get
$ dart doc .
Documenting my_package...
...
Success! Docs generated into /Users/me/projects/my_package/doc/api
```

기본적으로, `dart doc`는 생성된 문서와 지원 파일을 `doc/api` 디렉토리에 저장합니다. 
출력 디렉토리를 변경하려면, `--output` 플래그로 경로를 지정합니다.

```console
$ dart doc --output=api_docs .
```

패키지 설정이나 문서 주석에 문제가 있는 경우, `dart doc`는 이를 오류나 경고로 출력합니다. 
생성된 문서를 저장하지 않고 문제를 테스트하려는 경우, `--dry-run` 플래그를 추가합니다.

```console
$ dart doc --dry-run .
```

### 생성 구성 {:#configure}

`dart doc`가 문서를 생성하는 방식을 구성하려면, 
패키지의 루트 디렉토리에 `dartdoc_options.yaml`이라는 파일을 만듭니다.

파일 형식과 지원되는 구성 옵션에 대해 자세히 알아보려면, 
[dart.dev/go/dartdoc-options-file][dartdoc-options]를 확인하세요.

{% comment %}
TODO: Document the long-term supported options here.
{% endcomment -%}

[dartdoc-options]: {{site.redirect.go}}/dartdoc-options-file

## 생성된 문서 보기 {:#view}

`dart doc`로 생성된 문서는 다양한 방법으로 볼 수 있습니다.

### 로컬 문서 보기 {:#view-local}

`dart doc`로 생성하거나, 온라인에서 다운로드한 API 문서를 보려면, HTTP 서버로 로드해야 합니다.

파일을 제공하려면, HTTP 서버를 사용하세요. pub.dev에서 [`package:dhttpd`][]를 사용하는 것을 고려하세요.

`package:dhttpd`를 사용하려면, 전역적으로 활성화한 다음, 실행하고 생성된 문서의 경로를 지정하세요. 
다음 명령은 패키지를 활성화한 다음, 실행하여 `doc/api`에 있는 API 문서를 제공합니다.

```console
$ dart pub global activate dhttpd
$ dart pub global run dhttpd --path doc/api
```

생성된 문서를 브라우저에서 읽으려면, 
`dhttpd`가 출력하는 링크(일반적으로 `http://localhost:8080`)를 엽니다.

[`package:dhttpd`]: {{site.pub-pkg}}/dhttpd

### 호스팅된 문서 보기 {:#view-hosted}

정적 웹 콘텐츠를 지원하는 모든 호스팅 서비스를 사용하여 생성된 API 문서를 온라인으로 호스팅할 수도 있습니다. 
두 가지 일반적인 옵션은 [Firebase 호스팅][Firebase hosting]과 [GitHub 페이지][GitHub pages]입니다.

[Firebase hosting]: https://firebase.google.com/docs/hosting
[GitHub pages]: https://pages.github.com/

### 패키지 문서 보기 {:#view-pub}

[pub.dev 사이트]({{site.pub}})는 업로드된 패키지의 공개 라이브러리에 대한 문서를 생성하고 호스팅합니다.

패키지의 생성된 문서를 보려면, 해당 페이지로 이동하여 페이지 오른쪽에 있는 정보 상자에서 **API 참조** 링크를 엽니다. 
예를 들어, `package:http`에 대한 API 문서는 [pub.dev/documentation/http]({{site.pub-api}}/http)에서 찾을 수 있습니다.

### 코어 라이브러리 문서 보기 {:#view-sdk}

`dart doc`는 Dart 코어 라이브러리에 대한 API 참조 문서를 생성하는 데에도 사용됩니다.

Dart SDK 참조 문서를 보려면, 개발 중인 Dart 릴리스 채널에 해당하는 api.dart.dev 링크를 방문하세요.

| 브랜치   | 생성된 문서                              |
|----------|---------------------------------------------|
| `stable` | [api.dart.dev/stable]({{site.dart-api}})    |
| `beta`   | [api.dart.dev/beta]({{site.dart-api}}/beta) |
| `dev`    | [api.dart.dev/dev]({{site.dart-api}}/dev)   |
| `main`   | [api.dart.dev/main]({{site.dart-api}}/main) |

{:.table .table-striped}

## 문제 해결 {:#troubleshoot}

`dart doc`로 생성된 문서에서 일반적인 문제를 식별하고 해결하려면, 다음 참조 섹션을 참조하세요.

### 검색창을 로드하지 못했습니다. {:#troubleshoot-search}

생성된 문서의 검색 바가 작동하지 않거나, 
"Failed to initialize search"와 유사한 텍스트가 포함된 경우, 
다음 시나리오 중 하나가 가능합니다.

1. 자신의 파일 시스템에서 문서에 액세스하고 있지만, HTTP 서버로 제공 및 로드되지 않습니다. 
   로컬 API 문서를 제공하는 방법을 알아보려면, [생성된 문서를 로컬에서 보는 방법](#view-local)을 확인하세요.
2. `dart doc`에서 생성된 `index.json` 파일이 없거나, 
   문서 디렉터리 또는 호스팅된 웹 서버에서 액세스할 수 없습니다. 
   문서를 다시 생성하고 호스팅 구성을 검증해 보세요.

### 사이드바를 로드하지 못했습니다. {:#troubleshoot-sidebar}

생성된 문서의 사이드바가 누락되었거나 "Failed to load sidebar"와 유사한 텍스트가 포함된 경우, 
다음 시나리오 중 하나가 가능합니다.

1. 자신의 파일 시스템에서 문서에 액세스하고 있지만, 문서가 HTTP 서버로 제공 및 로드되지 않습니다. 
   로컬 API 문서를 제공하는 방법을 알아보려면, [로컬 문서를 보는 방법](#view-local)을 확인하세요.
2. 생성된 문서의 base-href 동작이 구성되었습니다. 
   이 구성 옵션은 더 이상 사용되지 않으며 더 이상 사용해서는 안 됩니다. 
   옵션을 제거하고, 기본 동작인 `dart doc`을 사용해 보세요. 
   기본 동작으로 인해 생성된 문서의 링크가 끊어지는 경우 [문제를 제출하세요][file an issue].

[file an issue]: {{site.repo.dart.org}}/dartdoc/issues

### API 문서가 없습니다. {:#troubleshoot-missing}

문서가 있어야 할 API에 대해 생성된 문서를 찾거나 액세스할 수 없는 경우, 다음 시나리오 중 하나가 가능합니다.

1. 패키지가 찾고 있는 API를 공개 API로 노출하지 않습니다. 
   `dart doc`는 다른 패키지에서 가져와 사용할 수 있도록, 
   노출된 공개 라이브러리 및 멤버에 대한 문서만 생성합니다. 
   패키지의 공개 라이브러리 구성에 대한 자세한 내용은, 
   [공개 라이브러리][public library]에서 패키지 레이아웃 가이드를 확인하세요.
2. 액세스하려는 URL의 대문자가 올바르지 않습니다. 
   기본적으로 `dart doc`는 대소문자를 구분하고, 
   해당 소스 선언과 일치하며 `.html` 확장자를 갖는 파일 이름을 생성합니다. 
   URL이 이러한 예상과 일치하는지 확인해 보세요.

[public libraries]: /tools/pub/package-layout#public-libraries

### 아이콘이 있어야 할 곳에 텍스트가 있습니다. {:#troubleshoot-icons}

메뉴 및 테마 버튼과 같은 아이콘 대신 텍스트가 표시되면, 
브라우저가 Material Symbols 글꼴을 로드하지 못했을 가능성이 큽니다. 
이를 해결하기 위한 몇 가지 옵션은 다음과 같습니다.

1. Google Fonts 서버에 액세스할 수 있는 프록시를 사용해 보세요.
2. 생성된 페이지를 업데이트하여 로컬 버전의 글꼴을 사용합니다.
