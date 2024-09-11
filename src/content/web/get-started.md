---
# title: Build a web app with Dart
title: Dart로 웹 앱 만들기
# description: Get started writing web apps in Dart.
description: Dart로 웹앱 작성을 시작하세요.
---

이 페이지에서는 Dart로 **웹 전용** 앱을 개발하기 시작하는 단계를 설명합니다. 
**멀티 플랫폼** 앱을 작성하려면, [Flutter를 사용해 보세요]({{site.flutter}}/web).

시작하기 전에 [Dart 소개](/language)를 읽어, Dart 기본 사항에 익숙해지세요. 
그런 다음, 아래 단계에 따라 Dart로 작은 웹 앱을 만드세요.

## 1. Dart 설치 {:#install-dart}

{% include 'get-sdk.md' %}

## 2. CLI 도구 또는 IDE(또는 둘 다)를 얻으세요. {:#tools}

<i class="material-symbols">terminal</i>
명령줄을 사용하려면, [`webdev`][] 패키지를 설치하세요.

```console
$ dart pub global activate webdev
```

<i class="material-symbols">web</i>
IDE 사용은 선택 사항이지만, IDE 사용을 적극 권장합니다. 
사용 가능한 IDE 리스트는 [편집기 및 디버거 개요][overview of editors & debuggers]를 참조하세요.

[overview of editors & debuggers]: /tools#editors

## 3. 웹 앱 만들기 {:#create}

<i class="material-symbols">terminal</i>
명령줄에서 웹앱을 만들려면, 
`web` 템플릿과 함께 [`dart create`][] 명령을 사용합니다.

```console
$ dart create -t web quickstart
```

<i class="material-symbols">web</i>
Dart 통합이 있는 IDE에서 동일한 웹 앱을 만들려면, 
**Bare-bones Web App**이라는 템플릿을 사용하여, 
[프로젝트를 만듭니다]({{site.flutter}}/tools/vs-code#creating-a-new-project).

웹 앱 템플릿은 모던 웹을 위해 빌드된, 
Dart의 강력하고 간결한 웹 상호 운용 솔루션인 [`package:web`][]을 import 합니다. 
이에 대해 자세히 알아보려면, [웹 상호 운용 개요](/interop/js-interop/package-web)를 확인하세요.

[`dart create`]: /tools/dart-create
[`package:web`]: {{site.pub-pkg}}/web

## 4. 앱을 실행하세요 {:#run}

<i class="material-symbols">terminal</i>
명령줄에서 앱을 실행하려면, [`webdev`][]를 사용하여 앱을 빌드하고 제공(serve)합니다.

```console
$ cd quickstart
$ webdev serve
```

<i class="material-symbols">web</i>
또는 IDE에서 앱을 실행합니다.

앱을 보려면, Chrome 브라우저를 사용하여 앱의 URL을 방문하세요. 
예를 들어, [`localhost:8080`](http://localhost:8080)입니다.

IDE나 명령줄을 사용하든, [`webdev serve`][]는 개발 JavaScript 컴파일러를 사용하여 앱을 빌드하고 제공합니다. 
개발 컴파일러가 앱을 처음 빌드하고 제공할 때 시작이 가장 느립니다. 
그 후에는, assets이 디스크에 캐시되고 증분 빌드(incremental builds)가 훨씬 빠릅니다.

앱이 컴파일되면, 브라우저에 "Your Dart app is running."가 표시됩니다.

![Launched bare-bones app](/assets/img/bare-bones-web-app.png){:width="500"}

[`webdev serve`]: /tools/webdev#serve

## 5. 앱에 커스텀 코드 추가 {:#add-code}

방금 만든 앱을 커스터마이즈해 보겠습니다.

1. 다음 스니펫에서 `thingsTodo()` 함수를 `web/main.dart` 파일에 복사합니다.

   ```dart
   Iterable<String> thingsTodo() sync* {
     const actions = ['Walk', 'Wash', 'Feed'];
     const pets = ['cats', 'dogs'];
   
     for (final action in actions) {
       for (final pet in pets) {
         if (pet != 'cats' || action == 'Feed') {
           yield '$action the $pet';
         }
       }
     }
   }
   ```

2. `newLI()` 함수를 추가합니다. (아래에 표시된 대로)
   지정된 `String`을 포함하는 새로운 `LIElement`를 만듭니다.

   ```dart
   Iterable<String> thingsTodo() sync* { /* ... */ }

   [!HTMLLIElement newLI(String itemText) =>!]
     [!(document.createElement('li') as HTMLLIElement)..text = itemText;!]
    
   void main() { /* ... */ }
   ```

3. `main()` 함수에서, `appendChild`와 `thingsTodo()`의 값을 사용하여, 
   `output` 요소에 콘텐츠를 추가합니다.

   ```dart
   Iterable<String> thingsTodo() sync* { /* ... */ }

   HTMLLIElement newLI(String itemText) =>
     (document.createElement('li') as HTMLLIElement)..text = itemText;

   void main() {
    final output = querySelector('#output');
    [!for (final item in thingsTodo()) {!]
      [!output?.appendChild(newLI(item));!]
    [!}!]
   }
   ```

4. 변경 사항을 저장합니다.

5. `webdev` 도구는 자동으로 앱을 다시 빌드합니다. 
   앱의 브라우저 창을 새로 고칩니다. 
   이제 간단한 Dart 앱에 todo 리스트가 생겼습니다! 다음과 같이 보일 것입니다.<br>

   ![Running the revised app](/assets/img/bare-bones-todo.png){:width="500"}

6. 원하는 경우 `web/styles.css`를 편집하여 서식을 개선한 다음, 앱을 다시 로드하여 변경 사항을 확인하세요.

   ```css
   #output {
     padding: 20px;
     [!text-align: left;!]
   }
   ```


## 6. Dart DevTools를 사용하여 앱을 검사합니다. {:#devtools}

Dart DevTools를 사용하여 중단점을 설정하고, 값과 타입을 보고, 
앱의 Dart 코드를 단계별로 살펴보세요. 
설정 세부 정보와 연습은 [Dart 웹 앱 디버깅][Debugging Dart Web Apps]을 참조하세요.

[Debugging Dart Web Apps]: /web/debugging

## 7. 웹 앱 빌드 및 배포 {:#deploy}

개발 환경 외부에서 웹 앱을 실행하려면, 빌드하고 배포해야 합니다. 
Dart 웹 앱 배포에 대해 자세히 알아보려면, [웹 배포][Web deployment]를 확인하세요.

[Web deployment]: /web/deployment

## 다음은 무엇입니까? {:#what-next}

다음 리소스를 확인하세요.

* Dart 언어, 라이브러리 및 규칙
  * [언어 투어](/language)
  * [핵심 라이브러리 연습](/libraries)
  * [효과적인 Dart](/effective-dart)
* 웹 개발
  * [JavaScript 상호 운용성](/interop/js-interop)
  * [웹 라이브러리 및 패키지](/web/libraries)
  * [`package:web` 개요](/interop/js-interop/package-web)
  * [DOM 소개][Introduction to the DOM]
* Dart [튜토리얼](/tutorials)

문제가 생기면 [커뮤니티 및 지원](/community)에서 도움을 받으세요.

[Introduction to the DOM]: https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction

[`webdev`]: /tools/webdev
