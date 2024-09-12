---
title: dart pub unpack
description: 패키지를 다운로드하고 해당 위치에 내용을 압축 해제합니다.
---

:::version-note
`unpack` 하위 명령은 Dart 3.4에서 도입되었습니다. 
이전 SDK가 포함된 패키지의 아카이브를 다운로드하려면, 
[pub.dev 사이트]({{site.pub}})에서 패키지의 **Versions** 탭을 방문하세요.
:::

_Unpack_ 은 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub unpack <package>[:descriptor] [--[no-]resolve] [--output=<output directory>] [--[no-]force] [other options]
```

이 명령은 지정된 `<package>`를 다운로드하고, 
그 내용을 `<package>-<version>` 디렉토리로 압축해제 합니다.

예를 들어, 다음 명령은 [pub.dev 사이트]({{site.pub}})에서, 
최신 stable `package:http` 버전을 다운로드하여 현재 디렉토리로 압축해제 합니다.

```console
$ dart pub unpack http
```
다운로드한 패키지의 소스나 버전을 변경하려면 패키지 이름과 콜론 뒤에 소스 설명자를 추가합니다. 
예를 들어, 다음 명령은 pub.dev 사이트에서 `package:http`의 `1.2.0` 릴리스를 다운로드합니다.

```console
$ dart pub unpack http:1.2.0
```

소스 설명자는 `dart pub add`와 동일한 구문으로 더 많은 구성을 지원합니다. 
소스 설명자와 해당 구문에 대해 자세히 알아보려면, 
`dart pub add`에 대한 [소스 설명자][source descriptor] 문서를 확인하세요.

[source descriptor]: /tools/pub/cmd/pub-add#source-descriptor

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 확인하세요.

### `--force` 또는 `-f` {:#force-option}

압축해제하는 동안 패키지 폴더나 그 내용과 충돌하는 기존 폴더를 덮어씁니다.

### `--[no-]resolve` {:#resolve-option}

기본적으로, `dart pub get`은 패키지를 다운로드하고 압축 해제한 후, 
자동으로 실행되어 패키지 해결(resolution)을 완료합니다. 
자동 해결을, 비활성화하려면 `--no-resolve` 플래그를 지정합니다.

```console
$ dart pub unpack http --no-resolve
```

### `--output=<dir>` 또는 `-o <dir>` {:#output-option}

기본적으로 패키지를 현재 디렉토리(`.`)로 추출합니다. 
패키지가 추출되는 디렉토리를 변경하려면, `--output` 옵션으로 원하는 출력 디렉토리를 지정합니다.

예를 들어, 다음 명령은 `package:http`의 `1.2.0` 릴리스를 `local_http_copies` 디렉토리로 압축 해제합니다.

```console
$ dart pub unpack http:1.2.0 -o local_http_copies
```

{% render 'pub-problems.md' %}
