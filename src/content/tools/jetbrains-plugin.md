---
title: IntelliJ & Android Studio
# description: Use Dart with a variety of IDEs and editors from JetBrains.
description: JetBrains의 다양한 IDE와 편집기로 Dart를 사용하세요.
---

[Dart 플러그인][Dart plugin]은 JetBrains에서 개발한 IntelliJ 플랫폼 기반 IDE에 Dart 지원을 추가합니다. 
이러한 IDE는 특정 개발 기술에 고유한 기능을 제공합니다. 
Dart 및 Flutter 개발에 권장되는 IDE는 다음과 같습니다.

- [IntelliJ IDEA][]
  - JVM 기반 언어 개발을 전문으로 하는 [IntelliJ IDEA][]
- [WebStorm][]
  - 웹 앱 개발을 전문으로 하는 [WebStorm][]
- [Android Studio][]
  - Android 및 Flutter 개발을 전문으로 하는 [Android Studio][]

Dart 개발을 위해 어떤 JetBrains IDE를 선택하든, 
이 페이지에는 빠르게 시작하고 필요할 때 더 많은 정보를 찾을 수 있도록 도와주는 리소스가 있습니다.

[IntelliJ IDEA]: https://www.jetbrains.com/idea/
[WebStorm]: https://www.jetbrains.com/webstorm/
[Android Studio]: {{site.android-dev}}/studio

## 시작하기 {:#getting-started}

IDE와 Dart SDK가 아직 없다면 받으세요.
그런 다음, Dart 플러그인을 설치하고 Dart SDK를 찾을 위치를 알려주세요.


### IDE 다운로드 {:#downloading-the-ide}

JetBrains IDE가 없다면 설치하세요. 다음 중 하나를 선택하세요.

* [IntelliJ IDEA][IDEA-Install]{:target="_blank" rel="noopener"}
* [IntelliJ IDEA EAP][IDEA-EAP-Install]{:target="_blank" rel="noopener"}
  (최신 Dart 언어 기능 및 IntelliJ 기능에 대한 조기 액세스를 위해)
* [WebStorm][WS-Install]{:target="_blank" rel="noopener"}
* [Android Studio][AS-Install]{:target="_blank" rel="noopener"}
* [다른 JetBrains 제품][Other]{:target="_blank" rel="noopener"}

[IDEA-Install]: https://www.jetbrains.com/idea/download/
[IDEA-EAP-Install]: https://www.jetbrains.com/idea/nextversion/
[WS-Install]: https://www.jetbrains.com/webstorm/download/
[AS-Install]: {{site.android-dev}}/studio/install
[Other]: https://www.jetbrains.com/products.html

:::note
IntelliJ IDEA 커뮤니티 에디션의 기능은 제한적입니다. 
예를 들어, 웹 앱 디버깅을 직접 지원하지 않습니다. 
또한 JavaScript, HTML, CSS 및 YAML에 대한 지원이 거의 없습니다.
:::


### Dart SDK 다운로드 {:#downloading-the-dart-sdk}

아직 Dart SDK가 없다면 설치하세요. 
Dart SDK만 따로 받거나, 전체 Dart SDK가 포함된 Flutter SDK를 다운로드하여 받을 수 있습니다.

다음 중 하나를 선택하세요.

* [Dart SDK 다운로드](/get-dart)
* [Flutter SDK 다운로드]({{site.flutter-docs}}/get-started/install)


### Dart 지원 구성 {:#configuring-dart-support}

Dart 지원을 구성하는 한 가지 방법은 다음과 같습니다.

<ol>
<li>
  <p>
    IDE를 시작하고 <b>Dart</b> 플러그인을 설치합니다.
  </p>

  <ol type="a">
    <li>Welcome 화면에서, <b>Plugins</b>을 선택합니다.</li>
    <li><b>Dart</b>를 검색합니다.</li>
    <li>Dart 플러그인을 설치한 후, IDE를 다시 시작합니다.</li>
  </ol>
</li>
<br>

<li>
  <p>
    새로운 Dart 프로젝트를 만드세요:
  </p>

  <ol type="a">
    <li>Welcome 화면에서 <b>New Project</b>를 클릭합니다.</li>
    <li>다음 대화 상자에서, <b>Dart</b>를 클릭합니다.</li>
  </ol>
</li>
<br>

<li>
  <p>
    <b>Dart SDK</b> 경로에 대한 값이 보이지 않으면, 값을 입력하거나 선택하세요.
  </p>

  <p>
    예를 들어, SDK 경로는 <code><em>&lt;dart 설치 디렉토리></em>/dart/dart-sdk</code> 일 수 있습니다.
  </p>

  :::note
  **Dart SDK**는 SDK의 `bin` 및 `lib` 디렉토리를 포함하는 디렉토리를 지정합니다. 
  `bin` 디렉토리에는 `dart` 및 `dartaotruntime`과 같은 도구가 포함됩니다. 
  IDE는 경로가 유효한지 확인합니다.
  :::
</li>

<li>
  <p>
    시작 템플릿을 선택하세요.
  </p>

  <ol type="a">
    <li>시작 템플릿을 활성화하려면, <b>Generate sample content</b>을 클릭합니다.</li>
    <li>원하는 템플릿을 선택합니다.</li>
  </ol>

  :::note
  제공된 템플릿은 [`dart create`](/tools/dart-create)에서 제공 및 생성됩니다.
  :::
</li>

<li>
  <p><b>Next</b>을 클릭하고, 프로젝트 설정을 계속하세요.</p>
</li>
</ol>

2단계의 대안은 기존 Dart 프로젝트를 열고, 
해당 프로젝트의 `pubspec.yaml` 파일이나 Dart 파일을 여는 것입니다.


## 보고된 문제 {:#reporting-issues}

공식 [Dart용 JetBrains 이슈 트래커][JetBrains issue tracker for Dart.]를 통해, 문제와 피드백을 보고해 주세요.

예상 동작, 실제 동작, 적절한 경우 스크린샷에 대한 세부 정보를 포함하세요.

[JetBrains issue tracker for Dart.]: https://youtrack.jetbrains.com/issues?q=Subsystem:%20%7BLang.%20Dart%7D%20

## 더 많은 정보 {:#more-information}

자세한 내용은 JetBrains 웹사이트를 참조하세요.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/)
  * [Dart WebStorm 도움말](https://www.jetbrains.com/help/webstorm/dart.html)
  * [기능](https://www.jetbrains.com/idea/features/)
  * [빠른 시작](https://www.jetbrains.com/help/idea/getting-started.html)
* [JetBrains의 Dart 플러그인][Dart plugin]

[Dart plugin]: https://plugins.jetbrains.com/plugin/6351-dart/
