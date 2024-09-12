---
title: dart create
# description: Command-line tool for creating Dart projects.
description: Dart 프로젝트를 만드는 명령줄 도구.
toc: false
---

`dart create` 명령은 여러 지원되는 템플릿 중 하나를 사용하여 Dart 프로젝트를 만듭니다. 
동일한 기능이 IDE에서 제공됩니다.

{% render 'tools/dart-tool-note.md' %}

`dart create`를 실행하면, 먼저 프로젝트 파일이 있는 디렉토리를 만듭니다. 
그런 다음, 패키지 종속성을 가져옵니다. (`--no-pub` 플래그를 지정하지 않는 한)

다음은 `dart create`를 사용하여, 
간단한 콘솔 앱(기본 템플릿)이 포함된 `my_cli`라는 디렉토리를 만드는 예입니다.

```console
$ dart create my_cli
```

`web`과 같은 다른 템플릿을 사용하려면, 템플릿 인수를 추가합니다.

```console
$ dart create -t web my_web_app
```

다음 표는 사용할 수 있는 템플릿을 보여줍니다.

| 템플릿       | 설명                                                                                           |
|----------------|-------------------------------------------------------------------------------------------------------|
| `cli`          | [`package:args`]({{site.pub-pkg}}/args)를 사용하여 기본 인수 구문 분석을 제공하는 명령줄 애플리케이션입니다. |
| `console`      | 명령줄 응용 프로그램.                                                                           |
| `package`      | 공유 Dart 라이브러리를 포함하는 패키지입니다.                                                           |
| `server-shelf` | [shelf][]를 사용하여 구축된 서버입니다.                                                                       |
| `web`          | Dart 코어 라이브러리를 사용하여 구축된 웹앱입니다.                                                            |

{:.table .table-striped .nowrap}

[shelf]: {{site.pub-pkg}}/shelf

이러한 템플릿은 [패키지 레이아웃 규칙](/tools/pub/package-layout)을 따르는 파일 구조를 생성합니다.

지정된 디렉토리가 이미 있는 경우, `dart create`가 실패합니다. 
`--force` 플래그로 프로젝트 생성을 강제할 수 있습니다.

```console
$ dart create --force <DIRECTORY>
```

명령줄 옵션에 대한 자세한 내용을 보려면, `--help` 플래그를 사용하세요.

```console
$ dart create --help
```

{% comment %}
```
Create a new Dart project.

Usage: dart create [arguments] <directory>
-h, --help                       Print this usage information.
-t, --template                   The project template to use.

          [console] (default)    A command-line application.
          [package]              A package containing shared Dart libraries.
          [server-shelf]         A server app using `package:shelf`
          [web]                  A web app that uses only core Dart libraries.

    --[no-]pub                   Whether to run 'pub get' after the project has been created.
                                 (defaults to on)
    --force                      Force project generation, even if the target directory already exists.
```
{% endcomment %}
