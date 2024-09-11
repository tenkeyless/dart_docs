---
# title: "Get started: Command-line and server apps"
title: "시작하기: 명령줄 및 서버 앱"
# description: Get Dart, run and compile a small app.
description: Dart를 다운로드해서 작은 앱을 실행하고 컴파일해 보세요.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
prevpage:
  url: /tutorials/server
  # title: Dart command-line and server tutorials
  title: Dart 명령줄 및 서버 튜토리얼
nextpage:
  url: /tutorials/server/cmdline
  # title: Write command-line apps
  title: 명령줄 앱 작성
---

다음 단계에 따라 Dart SDK를 사용하여 명령줄 및 서버 앱을 개발하세요. 
먼저 브라우저에서 Dart 언어를 사용해 보세요. 다운로드할 필요가 없습니다. 
그런 다음 Dart SDK를 설치하고, 작은 프로그램을 작성하고, Dart VM을 사용하여 해당 프로그램을 실행합니다. 
마지막으로 AOT(_ahead of time_) 컴파일러를 사용하여, 
완성된 프로그램을 네이티브 머신 코드로 컴파일하고, 
Dart 런타임을 사용하여 실행합니다.

## 1. DartPad에서 Dart 코드로 놀아보세요 {:#1-play-with-dart-code-in-dartpad}

[DartPad](/tools/dartpad)를 사용하면, 다운로드 없이도 Dart 언어와 API를 실험할 수 있습니다.

예를 들어, 작은 Hello World 프로그램의 코드를 가지고 놀 수 있는 내장형 DartPad가 있습니다. 
**Run**을 클릭하여 앱을 실행하면 콘솔 뷰에 출력이 나타납니다. 
소스 코드를 편집해 보세요. 인사말을 다른 언어로 변경하고 싶을 수도 있습니다.

:::note
{% render 'dartpad-embedded-troubleshooting.md' %}
:::

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dartpad
void main() {
  print('Hello, World!');
}
```

자세한 정보:

* [DartPad 문서][DartPad documentation]
* [Dart 언어 투어][Dart language tour]
* [Dart 코어 라이브러리 문서][Dart core library documentation]

## 2. Dart 설치 {:#2-install-dart}

{% include 'get-sdk.md' %}

## 3. 작은 앱 만들기 {:#3-create-a-small-app}

[`dart create`](/tools/dart-create) 명령과 `console` 템플릿을 사용하여 명령줄 앱을 만듭니다.

```console
$ dart create -t console cli
```

이 명령은 다음을 포함하는 작은 Dart 앱을 만듭니다.

* 메인 Dart 소스 파일 `bin/cli.dart`
  * 최상위 `main()` 함수를 포함하는, 메인 Dart 소스 파일 `bin/cli.dart`. 이것은 앱의 진입점입니다.
* 추가 Dart 파일 `lib/cli.dart`
  * 앱의 기능을 포함하고, `cli.dart` 파일에서 가져오는, 추가 Dart 파일 `lib/cli.dart`.
* pubspec 파일 `pubspec.yaml`
  * 앱의 메타데이터를 포함하는 pubspec 파일 `pubspec.yaml`. 
  * 여기에는 앱이 종속된 [패키지](/guides/packages)와 필요한 패키지 버전에 대한 정보가 포함됩니다.

:::note
후드 아래에서, `dart create`는 생성된 pubspec 파일을 스캔하고 종속성을 다운로드하는, 
[`dart pub get`][]을 실행합니다. 
pubspec 파일에 다른 종속성을 추가한 경우, `dart pub get`을 실행하여 다운로드합니다.
:::

[`dart pub get`]: /tools/pub/cmd/pub-get

## 4. 앱을 실행하세요 {:#4-run-the-app}

명령줄에서 앱을 실행하려면, 
앱의 최상위 디렉토리에서 [`dart run`](/tools/dart-run) 명령을 실행하여, 
Dart VM을 사용하세요.

```console
$ cd cli
$ dart run
Hello world: 42!
```

디버깅 지원으로 앱을 실행하려면, [Dart DevTools](/tools/dart-devtools)를 참조하세요.

## 5. 앱을 수정하세요 {:#5-modify-the-app}

방금 만든 앱을 커스터마이즈해 보겠습니다.

1. `lib/cli.dart`를 편집하여 다른 결과를 계산합니다. 예를 들어, 이전 값을 2로 나눕니다.
   (`~/`에 대한 자세한 내용은 [산술 연산자][Arithmetic operators] 참조):

    <?code-excerpt "misc/test/tutorial/get_started.dart (calculate)" replace="/~\/ 2/[!$&!]/g"?>
    ```dart
    int calculate() {
      return 6 * 7 [!~/ 2!];
    }
    ```

2. 변경 사항을 저장합니다.

3. 앱의 메인 진입점을 다시 실행합니다.

    ```console
    $ dart run
    Hello world: 21!
    ```

추가 정보: [명령줄 앱 작성](/tutorials/server/cmdline)

## 6. 프로덕션을 위해 컴파일 {:#6-compile-for-production}

위의 단계는 Dart VM(`dart`)을 사용하여 앱을 실행했습니다. 
Dart VM은 개발 중에 즉각적인 피드백을 제공하기 위해, 
빠르고 증분적인 컴파일을 위해 최적화되었습니다. 
이제 작은 앱이 완성되었으므로, 
Dart 코드를 최적화된 네이티브 머신 코드로 AOT 컴파일할 시간입니다.

`dart compile` 도구를 사용하여 프로그램을 머신 코드로 AOT 컴파일합니다.

```console
$ dart compile exe bin/cli.dart
```

컴파일된 프로그램이 즉시 시작되어, 빠르게 완료되는 것을 확인하세요.

```console
$ time bin/cli.exe
Hello world: 21!

real	0m0.016s
user	0m0.008s
sys	0m0.006s
```

## 다음은 무엇입니까? {:#what-next}

다음 리소스를 확인하세요.

* Dart [튜토리얼](/tutorials)
* Dart 언어, 라이브러리 및 규칙
  * [언어 투어](/language)
  * [Dart 코어 라이브러리 문서](/libraries)
  * [효과적인 Dart](/effective-dart)
* 도구 및 라이브러리
  * [Dart SDK](/tools/sdk)
  * [Dart 도구](/tools)
  * [IDE](/tools#editors)
* 네이티브 컴파일 앱의 다른 예
  * [native_app]({{site.repo.dart.org}}/samples/tree/main/native_app)

문제가 발생하면 [커뮤니티 및 지원](/community)에서 도움을 받으세요.

[Arithmetic operators]: /language/operators#arithmetic-operators
[DartPad documentation]: /tools/dartpad
[Dart language tour]: /language
[Dart core library documentation]: /libraries
[ide]: /tools#editors

