---
title: Dart FAQ
short-title: FAQ
# description: You have questions about Dart, we have answers.
description: Dart에 대한 질문이 있으면, 답변해 드리겠습니다.
---

{% assign pdf = '<i style="vertical-align: text-top" class="material-symbols">picture_as_pdf</i>' %}
{% assign site-repo = site.repo.this %}
{% assign sdk-repo = site.repo.dart.sdk %}
{% assign lang-repo = site.repo.dart.lang %}
{% assign ecma-pdf = 'https://ecma-international.org/wp-content/uploads' %}

이 페이지는 커뮤니티에서 가장 많이 질문된 질문 중 일부를 모아놓았습니다.

## 일반 {:#general}

### Q. Dart에 대한 사양이 있나요? {:#q-is-there-a-specification-for-dart}

네. [EMCA-408][emca408]은 Dart 프로그래밍 언어 사양을 다룹니다.

5가지 버전이 공개되었습니다. 최신 진행 중인 버전은 Dart 2.13-dev를 다룹니다.

| 에디션               | 게시됨         | 승인됨      | 버전에 대한 커버 |
|-----------------------|-------------------|---------------|-------------------|
| [6th][6th-ed] {{pdf}} | 2024년 1월 24일  |               | 2.13-dev          |
| [5th][5th-ed] {{pdf}} | 2021년 4월 9일     |               | 2.10              |
| [4th][4th-ed] {{pdf}} | 2015년 8월 19일   | 2015년 12월 | 1.11              |
| [3rd][3rd-ed] {{pdf}} | 2015년 4월 15일    | 2015년 6월     | 1.9               |
| [2nd][2nd-ed] {{pdf}} | 2014년 11월 21일 | 2014년 12월 | 1.6               |
| [1st][1st-ed] {{pdf}} | 2014년 3월 27일    | 2014년 6월     | 1.3               |

{:.table .table-striped}

사양에 대해 자세히 알아보려면, [Dart 언어 사양](/guides/language/spec) 페이지를 검토하세요.

### Q. Dart의 변경 사항에 대한 의견을 어떻게 받아들이고 있나요? {:#q-how-are-you-taking-input-on-changes-to-dart}

팀은 피드백을 듣고, [문제][SDK issues]를 읽고, 기여자의 패치를 검토합니다. 
실적이 좋은 기여자는 저장소에 대한 쓰기 권한을 부여받을 수 있습니다. 
Google 엔지니어도 공개 저장소에서 작업하여, 눈에 띄는 변경 사항을 만듭니다. 
이 프로젝트는 많은 외부 패치를 받았으며, 분산된 커미터를 환영합니다.

[emca408]: https://ecma-international.org/publications-and-standards/standards/ecma-408/
[1st-ed]: {{ecma-pdf}}/ECMA-408_1st_edition_june_2014.pdf
[2nd-ed]: {{ecma-pdf}}/ECMA-408_2nd_edition_december_2014.pdf
[3rd-ed]: {{ecma-pdf}}/ECMA-408_3rd_edition_june_2015.pdf
[4th-ed]: {{ecma-pdf}}/ECMA-408_4th_edition_december_2015.pdf
[5th-ed]: /guides/language/specifications/DartLangSpec-v2.10.pdf
[6th-ed]: https://spec.dart.dev/DartLangSpecDraft.pdf

---

## 언어 {:#language}

### Q. Dart는 Java와 많이 비슷하지 않나요? {:#q-isnt-dart-a-lot-like-java}

Dart는 Java와 몇 가지 유사점이 있습니다. 
친숙한 구문으로 간단한 예를 검토하려면, 
[Dart 소개](/language)의 코드 샘플을 검토해보세요.

### Q. Dart는 Go와 어떤 관련이 있나요? {:#q-how-does-dart-relate-to-go}

Google은 Dart와 Go 언어 프로젝트를 시작했습니다. 
이 독립적인 프로젝트는 목표가 다릅니다. 
결과적으로 서로 다른 선택을 합니다. 
언어는 매우 다른 특성을 가지고 있지만, 팀원들은 서로의 작업에서 배웁니다.

### Q. Dart가 Haskell/Smalltalk/Python/Scala/다른 언어와 더 비슷하지 않은 이유는 무엇입니까? {:#q-why-isnt-dart-more-like-haskell-smalltalk-python-scala-other-language}

비교 언어에 따라 다양한 이유.

**언어가 JavaScript와 다름**
: Dart는 효율적인 JavaScript로 컴파일해야 합니다. 
  JavaScript와 너무 다른 소스 언어는 소스 언어의 동작을 에뮬레이트하기 위해, 
  복잡한 출력 코드를 생성할 수 있습니다. 
  이로 인해 프로그래머에게 명확하지 않은 방식으로 성능이 달라질 수 있습니다.

**네이티브 코드로 컴파일되는 언어**
: Dart는 기계 코드로의 효율적인 컴파일을 우선시합니다. 
  따라서, 다른 컴파일 언어와 몇 가지 측면을 공유합니다.

**Dart보다 "더 동적"으로 간주되는 언어**
: Dart는 더 나은 성능과 더 생산적인 도구를 얻기 위해, 
  이러한 임의의 런타임 수정 중 일부를 트레이드 오프하기로 선택합니다.

### Q. 왜 Dart 구문이 더 흥미롭지 않나요? {:#q-why-isnt-dart-syntax-more-exciting}

`this.` 생성자 인수와 한 줄 함수의 `=>`와 같은 멋진 구문적 특징이 있습니다. 
Dart는 _흥분_ 보다 _익숙함_ 을 선택합니다.

### Q. Dart에는 reflection 기능이 있나요? {:#q-does-dart-have-reflection-capabilities}

**서버 및 명령줄 스크립트**
: 예, Dart는 [mirrors API][dart-mirror]에서 reflection을 지원합니다.

**웹 또는 Flutter 앱**
: 아니요, Dart는 [웹 또는 Flutter 앱][Flutter no mirrors]에 대한 쓰기를 지원하지 않습니다.

### Q. Dart는 튜플을 추가하고, 부분 평가, ...를 할 수 있나요? {:#q-can-dart-add-tuples-partial-evaluation}

향후 릴리스에는 원하는 기능이 포함될 수 있습니다. 일부 기능은 언어의 특성에 맞지 않습니다. 
일부는 다른 기능과 잘 맞지 않습니다. 단순성은 미래 프로그래머에게 줄 수 있는 가장 중요한 선물입니다.

누군가가 요청을 제출했는지 확인하려면, 
[언어 퍼널(funnel)][lang-funnel] 및 [언어 문제 리스트][lang-issues]를 검토하세요.

* 문제가 있는 경우, 엄지손가락을 올리세요.
* 문제가 없는 경우, [새로운 문제][new-issue]를 요청하세요.

  기능에 대한 사려 깊은 주장을 하세요. 주장에 증거를 추가하세요. 
  기능이 있는 샘플 코드와 없는 샘플 코드 또는 상당한 코드베이스를 포함하세요.

자세한 내용은, [언어 진화 프로세스][lang-process]를 참조하세요.

Dart 언어팀이 당신의 요청을 거부하더라도 놀라지 마십시오. 
언어 기능을 제거하는 것은 기능을 추가하는 것보다 더 큰 고통을 줍니다. 
Dart 언어팀은 가장 눈에 띄는 기능을 먼저 추가하고, 나중에 다음 단계를 다시 살펴봅니다.

커뮤니티는 어떤 단일 언어도 완전히 엉망으로 만들지 않고는 충족할 수 없는 많은 기능을 요청할 것입니다. 
Dart 언어팀은 제안과 증거를 소중히 여깁니다. 
이러한 감사는 신중한 디자인 선택과 이에 대한 공정한 의사소통을 통해 분명해질 것입니다.

---

## 타입 {:#typing}

### Q. Dart는 정적 타입을 사용하나요? {:#q-does-dart-use-static-typing}

네, Dart는 정적 타입을 사용합니다. 자세한 내용은 [Dart의 타입 시스템][Dart's type system]을 참조하세요.

정적 및 런타임 검사를 결합한 Dart는 sound 타입 시스템을 갖추고 있습니다. 
이는 한 타입의 표현식이 다른 타입의 값을 생성할 수 없음을 보장합니다.

동적 타이핑의 유연성이 필요한 경우, 모든 변수에 `dynamic` 주석을 달 수 있습니다. 
이 `dynamic` 타입은 정적이지만, _런타임_ 에 모든 타입을 포함할 수 있습니다. 
이는 해당 변수에서 타입 안전 언어의 많은 이점을 제거합니다.

### Q. 제네릭이 covariant인 이유는 무엇입니까? {:#q-why-are-generics-covariant}

covariant 제네릭은 프로그래머가 가진 일반적인 직관에 부합하며, 이 직관은 종종 옳습니다. 
예를 들어, 제네릭의 일반적인 "읽기 전용" 사용에서와 같습니다. 
이 직관이 항상 옳지는 않지만, Dart는 covariant 제네릭을 사용함으로써, 편의성을 추구하고 있습니다.

다른 합리적인 기본 변형(variance)은 불변성(invariance)뿐입니다. 
불변적 제네릭만 사용하면 확실히 더 많은 오류를 방지할 수 있지만, 
많은 유효한 프로그램을 방지하거나 "사과" 리스트가 있고, 누군가가 "과일"만 원할 때마다 변환이 필요합니다.

우리는 언어가 변형을 표시하거나 추론하는 다양한 방법을 알고 있습니다. 
우리는 변형 추론 시스템이 Dart에서 이점을 위해 너무 많은 복잡성을 추가한다고 생각합니다.

다시 말하지만, 우리는 실용적이려고 노력하고 있으며, 그 결과가 합리적이라고 생각합니다.

---

## 사용법 및 도구 {:#usage-and-tools}

### Q. Dart는 JSON을 지원하나요? {:#q-does-dart-support-json}

네. 자세한 내용은 [dart:convert][] 라이브러리의 [JSON] 변환기를 참조하세요.

### Q. Dart를 서버에서 실행할 수 있나요? {:#q-can-dart-run-on-the-server}

네. 자세한 내용은 [서버의 Dart][Dart on the Server]를 참조하세요.

### Q. 타사 코드를 사용하거나 코드를 공유하려면 어떻게 해야 하나요?{:#q-how-do-i-use-third-party-code-or-share-code}

Dart 및 Flutter용 패키지 호스팅 서비스인 [pub.dev 사이트][pub]에서 패키지를 검색하세요. 
[`pub` 명령][pub-cmd]를 사용하여, 코드를 패키징하고 사이트에 업로드하세요.

### Q. Dart 코드를 작성하려면 특정 편집기나 IDE를 사용해야 합니까? {:#q-do-i-need-to-use-a-particular-editor-or-ide-to-write-dart-code}

아니요. [DartPad][]로 Dart 코드를 시도한 다음, 좋아하는 편집기나 IDE를 사용하여 개발할 수 있습니다. 
IntelliJ IDEA, WebStorm, Visual Studio Code와 같은 일부 모든 기능을 갖춘 IDE에는 Dart 플러그인이 있습니다. 
여러 편집기에 대한 오픈 소스 Dart 플러그인도 있습니다. 
자세한 내용은 [Dart 도구][Dart tools]를 참조하세요.

### Q. Dart로 안드로이드 앱을 만들 수 있나요? {:#q-can-i-build-an-android-app-with-dart}

네! [Flutter][Flutter] 프레임워크와 Dart 언어를 사용하여, Android 앱을 빌드할 수 있습니다. 
작성한 모든 Flutter 앱은 iOS, 웹 및 데스크톱 플랫폼에서도 작동합니다.

### Q. Dart의 실제 프로덕션 배포 사례에는 어떤 것들이 있나요? {:#q-what-are-some-real-world-production-deployments-of-dart}

Google Ads, AdSense, AdMob 및 Google Assistant는 Dart를 사용합니다. 
Google 수익의 상당 부분은 이러한 앱을 통해 흐릅니다. 
Google 내부 또는 외부에서 [모든 Flutter 앱][FlutterShowcase]는 Dart를 사용합니다.

[dart:convert]: {{site.dart-api}}/stable/dart-convert/dart-convert-library.html
[SDK issues]: {{sdk-repo}}/issues
[lang-issues]: {{lang-repo}}/issues
[lang-funnel]: {{lang-repo}}/projects/1
[lang-process]: {{lang-repo}}/blob/main/doc/life_of_a_language_feature.md
[pub]: {{site.pub}}
[announcement]: https://blog.chromium.org/2013/11/dart-10-stable-sdk-for-structured-web.html
[lang]: /language
[JSON]: {{site.dart-api}}/dart-convert/JsonCodec-class.html
[Dart on the Server]: /server
[Dart tools]: /tools/
[DartPad]: {{site.dartpad}}
[Flutter]: {{site.flutter}}
[Dart's type system]: /language/type-system
[Flutter no mirrors]: {{site.flutter-docs}}/resources/faq#does-flutter-come-with-a-reflection--mirrors-system
[FlutterShowcase]: {{site.flutter}}/showcase
[new-issue]: {{site-repo}}/issues/new/choose
[isolate]: {{site.dart-api}}/stable/dart-isolate/dart-isolate-library.html

---

## 네이티브 실행 {:#native-execution}

### Q. Dart는 단일 스레드인가요? {:#q-is-dart-single-threaded}

아니요. 네이티브 대상에서 [Dart의 isolate API][isolate]는 필요할 때 여러 실행 스레드를 시작할 수 있습니다. 
Dart VM은 여러 CPU 코어를 사용하여, 이러한 스레드를 동시에 실행합니다.

[Dart의 동시성 아키텍처](/language/concurrency)는 일반적인 공유 메모리 스레딩의, 
복잡하고 오류가 발생하기 쉬운 코드를 추상화합니다. 
이는 Dart가 단일 스레드라는 오해를 설명할 수 있습니다.

동시성은 Dart 웹 앱에서 다르게 작동합니다. 
자세한 내용은 [Dart가 웹에서 단일 스레드인가요?](#q-is-dart-single-threaded-on-the-web)를 참조하세요.

### Q. Dart 코드를 네이티브 코드로 컴파일할 수 있나요? {:#q-can-i-compile-dart-code-to-native-code}

네. 데스크톱이나 모바일과 같은 기기를 대상으로 하는 앱을 컴파일할 때, 
[Dart Native](/overview#native-platform)는 네이티브 코드를 생성하기 위해, 
JIT(Just-in-Time) 컴파일러와 AOT(Ahead-of-Time) 컴파일러가 있는 Dart VM을 모두 포함합니다.

[Flutter][] 프레임워크는 Dart의 네이티브 컴파일 기능을 사용하여, 빠른 네이티브 앱을 생성합니다.

### Q. 터미널에서 실행할 수 있는 Dart 프로그램을 컴파일할 수 있나요? {:#q-can-i-compile-a-dart-program-for-running-in-a-terminal}

네. Dart 프로그램은 macOS Terminal, Windows 명령 프롬프트 또는 Linux 셸에서 실행하기 위해, 
네이티브 코드로 컴파일할 수 있습니다.

[dart compile][] 문서를 참조하세요.

### Q. AOT로 컴파일한 코드와 JIT로 컴파일한 코드 중 어느 것이 더 빠를까요? {:#q-which-is-faster-aot-or-jit-compiled-code}

상황에 따라 다릅니다.
Dart가 코드를 컴파일하는 방식은 다양한 성능 특성을 가진 앱을 생성합니다.

* AOT 컴파일 코드는 일관된 런타임 성능으로 빠르게 시작되며, 초기 실행 시 지연이 없습니다.

* JIT 컴파일 코드는 느리게 시작되지만, 런타임 최적화를 적용할 만큼 충분히 오래 실행되면, 최고 성능에 도달합니다.

---

## 웹 실행 {:#web-execution}

### Q. JavaScript 컴파일 대상으로 어떤 브라우저를 지원하시나요? {:#q-what-browsers-do-you-support-as-javascript-compilation-targets}

_production_ 웹 컴파일러는 다음 브라우저의 마지막 두 가지 주요 릴리스를 지원합니다.

* Google Chrome
* Microsoft Edge
* Firefox
* Apple Safari

[_development_ JavaScript 컴파일러](/tools/webdev#serve)는 디버깅을 위해 Chrome만 지원합니다.

### Q. Dart는 웹에서 단일 스레드인가요? {:#q-is-dart-single-threaded-on-the-web}

다소. Dart 웹 앱은 isolates를 사용할 수 없습니다. 
코드 동시성을 달성하기 위해 웹 앱은 [웹 워커][web workers]를 사용합니다. 
웹 워커는 isolates의 용이성과 효율성이 부족하고 다른 기능과 제한이 있습니다. 
자세한 내용은 [웹에서의 동시성](/language/concurrency#concurrency-on-the-web)을 참조하세요.

[web workers]: https://developer.mozilla.org/docs/Web/API/Web_Workers_API/Using_web_workers

### Q. 유효한 Dart 코드가 JavaScript로 컴파일될 수 있나요? {:#q-will-any-valid-dart-code-compile-to-javascript}

모든 유효한 Dart 코드는 JavaScript로 컴파일해야 합니다. 
일부 라이브러리는 서버나 Flutter에서만 실행됩니다. 
`dart:io` 라이브러리를 고려해 보세요. 
브라우저에서 사용할 수 없는 API를 사용하여, 운영 체제 파일과 디렉토리에 액세스할 수 있습니다.

### Q. Dart가 JavaScript로 컴파일하는 방법이 두 가지인 이유는 무엇입니까? {:#q-why-does-dart-have-two-ways-to-compile-to-javascript}

두 방법 모두 `webdev` 명령을 사용합니다. 
`webdev build` 명령은 프로덕션에 최적화된 축소된 JavaScript를 생성합니다. 
`webdev serve` 명령은 디버깅에 최적화된 모듈화된 JavaScript를 생성합니다.

자세한 내용은 [Dart JavaScript 컴파일러 참조](/tools/dart-compile#js)를 참조하세요.

### Q. JavaScript로 컴파일할 때 부동 소수점 숫자는 어떻게 처리되나요? {:#q-how-are-floating-point-numbers-handled-when-compiled-to-javascript}

JavaScript에는 숫자 표현이 하나뿐입니다. 
IEEE-754 배정밀도 부동 소수점 숫자입니다. 
즉, 정수든 부동 소수점이든 모든 숫자는 double로 표현됩니다. 
JavaScript에는 형식이 지정된 데이터 배열이 있으며, 
네이티브 Dart 형식 리스트에서 JavaScript 형식 배열로의 매핑은 간단합니다.

### Q. Dart는 JavaScript로 컴파일할 때 정수를 어떻게 처리하나요? {:#q-how-does-dart-handle-integers-when-compiling-to-javascript}

JavaScript는 모든 [숫자를 double형으로 저장][number-js]합니다. 
이는 정수를 -2<sup>53</sup>에서 2<sup>53</sup>까지의 값을 갖는 53비트 정밀도로 제한합니다. 
JavaScript는 정확도를 잃지 않고, 이 범위 내에서 정수를 저장할 수 있습니다. 
JavaScript VM은 숫자의 내부 표현을 조작하므로, 작은 정수(SMI) 범위 내에 머물러야 합니다. 
JavaScript에서 해당 범위는 -2<sup>31</sup>에서 2<sup>31</sup>(0을 포함하여 -2,147,483,647에서 2,147,483,648) 사이입니다.

### Q. JavaScript로 컴파일할 때 타입화된 리스트는 어떻게 처리되나요? {:#q-how-are-typed-lists-handled-when-compiled-to-javascript}

JavaScript는 Dart의 타입화된 리스트와 호환되는 32비트 유형화된 배열을 제공합니다. 
이는 `Float32List`가 `Float32Array`가 되는 것으로 매핑됩니다. 
프로덕션 JavaScript 컴파일러는 64비트 정수인 `Int64List` 또는 `Uint64List`를 지원하지 않습니다. 
이러한 리스트 중 하나로 Dart 코드를 컴파일하면 런타임 예외가 발생합니다.

[number-js]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#number_type
[ppwsize]: https://work.j832.com/2012/11/excited-to-see-dart2js-minified-output.html
[package:js]: {{site.pub-pkg}}/js
[dart compile]: /tools/dart-compile
[dart analyze]: /tools/dart-analyze
[webdev]: /tools/webdev

[dart-mirror]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-mirrors
[pub-cmd]: /tools/pub/cmd
