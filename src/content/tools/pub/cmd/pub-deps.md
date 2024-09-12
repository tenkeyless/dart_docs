---
title: dart pub deps
description: dart pub deps를 사용하여 패키지의 종속성 그래프를 출력합니다.
---

_Deps_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```console
$ dart pub deps [--style=<style>] [--[no-]dev] [--executables]
```

이 명령은 패키지의 종속성 그래프를 출력합니다. 
그래프에는 패키지가 사용하는 [즉각적 종속성](/tools/pub/glossary#immediate-dependency)(pubspec에 지정된 대로)과, 
직접 종속성에서 가져온 [전이적 종속성](/tools/pub/glossary#transitive-dependency)이 모두 포함됩니다.

종속성 정보는 기본적으로 트리로 출력됩니다.

예를 들어, markdown_converter 예제의 pubspec은 다음 종속성을 지정합니다.

```yaml
dependencies:
  barback: ^0.15.2
  markdown: ^0.7.2
```

다음은 markdown_converter에 대한 `dart pub deps` 출력의 예입니다.

```console
$ dart pub deps
markdown_converter 0.0.0
|-- barback 0.15.2+6
|   |-- collection 1.1.2
|   |-- path 1.3.6
|   |-- pool 1.1.0
|   |   '-- stack_trace...
|   |-- source_span 1.2.0
|   |   '-- path...
|   '-- stack_trace 1.4.2
|       '-- path...
'-- markdown 0.7.2
```

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은, [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--style=<style>` 또는 `-s <style>` {:#stylestyle-or-s-style}

지정된 스타일은 출력 형식을 결정합니다:

`tree`
: 종속성 정보를 트리로 출력합니다. 이는 기본 형식입니다.

`list`
: 종속성 정보를 리스트로 출력합니다.

`compact`
: 종속성 정보를 컴팩트 리스트로 출력합니다.

### `--[no-]dev` {:#no-dev}

기본적으로 dev 종속성(`--dev`)을 포함한 모든 종속성을 출력합니다.
dev 종속성을 제거하려면, `--no-dev`를 사용합니다.

### `--executables` {:#executables}

사용 가능한 모든 실행 파일을 출력합니다.

### `--json` {:#json}

JSON 형식으로 출력을 생성합니다.


{% render 'pub-problems.md' %}
