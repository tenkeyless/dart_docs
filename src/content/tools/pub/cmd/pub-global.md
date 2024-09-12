---
title: dart pub global
# description: Use dart pub global to run Dart scripts hosted on the pub.dev site from the command line.
description: 명령줄에서 pub.dev 사이트에 호스팅된 Dart 스크립트를 실행하려면, dart pub global을 사용하세요.
---

_Global_ 은 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

Pub의 `global` 옵션을 사용하면, 현재 패키지 내부에 없을 때 명령줄에서 Dart 스크립트를 실행할 수 있습니다. 
[패키지를 활성화한](#activating-a-package) 후, 
해당 패키지의 `bin` 디렉터리에서 [스크립트를 실행](#running-a-script)할 수 있습니다. 
[패키지를 비활성화](#deactivating-a-package)하면, 
전역적으로 사용 가능한 패키지 리스트에서 해당 패키지가 제거됩니다.

예를 들어, [webdev][]를 사용하여 명령줄에서 Dart 웹 애플리케이션을 제공하려는 경우를 가정해 보겠습니다.

```console
$ dart pub global activate webdev
$ webdev serve
```

이것이 작동하지 않으면, [경로 설정](#running-a-script-from-your-path)이 필요할 수 있습니다.

패키지 내부에서 또는 패키지가 종속된 패키지에서 Dart 스크립트를 실행하려면, 
[dart run](/tools/dart-run)을 참조하세요.

## 패키지 활성화 {:#activating-a-package}

```plaintext
dart pub global activate [--noexecutables] [--executable=<name>] [--overwrite] <package> [version-constraint]
```

명령줄에서 하나 이상의 실행 파일을 실행할 수 있도록 하려는 경우 패키지를 활성화합니다. 
[pub.dev 사이트]({{site.pub}}), Git 저장소 또는 로컬 머신에 있는 패키지를 활성화할 수 있습니다. 
패키지를 활성화한 후, [스크립트 실행](#running-a-script)을 참조하여, 
패키지의 `bin` 디렉터리에서 스크립트를 실행합니다.

패키지를 활성화할 때, 선택적 버전 제약 조건을 지정할 수 있습니다. 
사용 예는 [제약 조건](#options) 플래그를 참조하세요.

### pub.dev 사이트에서 패키지 활성화 {:#activating-a-package-on-the-pub-dev-site}

```console
$ dart pub global activate <pub.dev package>
```

pub.dev 사이트에서 패키지를 지정하여 활성화합니다. 예를 들어:

```console
$ dart pub global activate markdown
```

### Git으로 패키지 활성화 {:#activating-a-package-with-git}

```console
$ dart pub global activate --source git <Git URL>
$ dart pub global activate -sgit <Git URL>
```

Git 저장소에서 패키지를 활성화하려면, `--source git`(또는 간단히 `-sgit`)을 사용합니다. 
[GitHub](https://github.com/)에서 `async_await` 패키지를 활성화하는 다음 예는 동일합니다.

```console
$ dart pub global activate --source git https://github.com/dart-lang/async_await.git
$ dart pub global activate -sgit https://github.com/dart-lang/async_await.git
```

Pub은 Git 저장소의 루트에서 패키지를 찾을 것으로 예상합니다. 
다른 위치를 지정하려면, 저장소 루트에 상대적인 경로와 함께 `--git-path` 옵션을 사용합니다.

```console
$ dart pub global activate -sgit https://github.com/dart-lang/http.git --git-path pkgs/http/
```

Pub은 Git 저장소의 기본 브랜치를 사용합니다. 
다른 브랜치나 커밋을 지정하려면 `--git-ref` 옵션을 사용합니다.

```console
$ dart pub global activate -sgit https://github.com/dart-lang/http.git --git-ref 36f98e900347335af2338a0e087538009b7de2f9
```

### 로컬 컴퓨터에서 패키지 활성화 {:#activating-a-package-on-your-local-machine}

```console
$ dart pub global activate --source path <path>
```

`activate --source path <path>`를 사용하여 로컬 머신에서 패키지를 활성화합니다. 
다음 예는 `~/dart` 디렉토리에서 `stopwatch` 패키지를 활성화합니다.

```console
$ dart pub global activate --source path ~/dart/stopwatch
```

### 활성화된 패키지 업데이트 {:#updating-an-activated-package}

패키지가 활성화되면, 패키지를 다시 활성화하여 업그레이드할 수 있습니다.

## 스크립트 실행 {:#running-a-script}

명령줄에서 활성화된 패키지의 스크립트를 직접 실행할 수 있습니다. 
스크립트를 직접 실행할 수 없는 경우, `dart pub global run`을 사용할 수도 있습니다.

### PATH에서 스크립트 실행 {:#running-a-script-from-your-path}

명령줄에서 직접 스크립트를 실행하려면, 
[시스탬 캐시][system cache] `bin` 디렉토리를 `PATH` 환경 변수에 추가합니다.

예를 들어, webdev 패키지를 활성화했지만, 여전히 명령을 실행할 수 없는 경우:

```console
$ dart pub global activate webdev
$ webdev serve
-bash: webdev: command not found
```

시스템 캐시의 `bin` 디렉토리가 경로에 있는지 확인하세요. 
macOS에서 다음 `PATH` 변수는 시스템 캐시를 포함합니다.

```console
$ echo $PATH
/Users/<user>/homebrew/bin:/usr/local/bin:/usr/bin:/bin:[!/Users/<user>/.pub-cache/bin!]
```

이 디렉토리가 `PATH`에 없는 경우, 플랫폼에 맞는 파일을 찾아 추가하세요.

| 플랫폼                             | 캐시 위치                 |
|--------------------------------------|--------------------------------|
| macOS 또는 Linux                       | `$HOME/.pub-cache/bin`         |
| Windows<sup><strong>*</strong></sup> | `%LOCALAPPDATA%\Pub\Cache\bin` |

{:.table .table-striped}

<sup><strong>*</strong></sup> 시스템 캐시의 정확한 위치는 Windows 버전마다 다를 수 있습니다.

이제 다음 명령을 직접 호출할 수 있습니다.

```console
$ cd web_project
$ [!webdev serve!]
```

스크립트가 여전히 명령줄에서 실행되지 않으면, 
패키지가 이 기능에 대해 [구성](#configuring-package-executables)되지 않았을 수 있습니다. 
`dart pub global run`을 사용하여, 스크립트를 실행할 수 있습니다.

### `dart pub global run`을 사용하여 스크립트 실행 {:#running-a-script-using-dart-pub-global-run}

```plaintext
$ dart pub global run <package>:<executable> [args...]
```

스크립트가 명령줄에서 실행되도록 구성되지 않았더라도, 
`dart pub global run`을 사용할 수 있습니다. 
다음 명령은 `foo` 패키지에서 `bin/bar.dart` 스크립트를 실행하여, 두 개의 인수를 전달합니다.

```console
$ dart pub global run foo:bar arg1 arg2
```

### 패키지 executables 구성 {:#configuring-package-executables}

패키지 개발자가 아니라면, 이 섹션을 건너뛸 수 있습니다.

패키지는 일부 스크립트를 명령줄에서 직접 실행할 수 있는 실행 파일로 노출할 수 있습니다. 
스크립트 또는 스크립트는 pubspec 파일의 [`executables`](/tools/pub/pubspec#executables) 항목에 나열되어야 합니다. 
예를 들어, 다음 pubspec 파일은 `bin/helloworld.dart`를 helloworld 패키지의 실행 파일로 식별합니다.

```yaml
name: helloworld

executables:
  helloworld:
```

`executables` 태그 아래에 스크립트를 나열하지 않으면, 스크립트의 유용성이 떨어집니다. 
나열되지 않은 스크립트는 `dart pub global run` 명령을 사용하여 실행할 수 있지만, 
명령줄에서 직접 실행할 수는 없습니다.

## 패키지 비활성화 {:#deactivating-a-package}

```console
$ dart pub global deactivate <package>
```

`deactivate`를 사용하여 사용 가능한 글로벌 패키지 리스트에서 패키지를 제거합니다. 예를 들어:

```console
$ dart pub global deactivate markdown
```

더 이상 `dart pub global run`이나 명령줄을 사용하여 패키지의 스크립트를 호출할 수 없습니다.

## 활성 패키지 나열 {:#listing-active-packages}

```console
$ dart pub global list
```

`list`를 사용하면 현재 활성화된 모든 패키지를 나열할 수 있습니다.

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `[version-constraint]`

`dart pub global activate <package> [version-constraint]`를 사용하여, 
패키지의 특정 버전을 지정합니다. 
예를 들어, 다음 명령은 `markdown` 패키지의 0.6.0 버전을 가져옵니다.

```console
$ dart pub global activate markdown 0.6.0
```

범위를 지정하면, pub는 해당 제약 조건을 충족하는 최상의 버전을 선택합니다. 예를 들어:

```console
$ dart pub global activate foo <3.0.0
```

### `--no-executables`

`dart pub global activate <package> --no-executables`를 사용하여, 
지정된 패키지를 전역적으로 활성화하지만, 실행 파일을 `bin`에 넣지 않습니다. 
실행 파일을 실행하려면, `dart pub global run`을 사용해야 합니다.

### `--executable=<name>` 또는 `-x <name>` {:#executablename-or-x-name}

`dart pub global activate`와 함께 사용하여, 
지정된 실행 파일을 PATH에 추가합니다. 
이러한 플래그 중 두 개 이상을 전달할 수 있습니다.

예를 들어, 다음 명령은 `bar`와 `baz`를 PATH에 추가합니다.
(`foo`가 정의할 수 있는 다른 실행 파일은 추가하지 않음)

```console
$ dart pub global activate foo -x bar -x baz
```

### `--overwrite`

`dart pub global activate <package> --overwrite`를 사용하여, 
이전에 활성화된 글로벌 실행 파일을 같은 이름으로 덮어씁니다. 
이 플래그를 지정하지 않으면, 기존 실행 파일은 대체되지 않습니다.

{% render 'pub-problems.md' %}

[system cache]: /tools/pub/glossary#system-cache
[webdev]: /tools/webdev
