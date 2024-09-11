실제 앱을 개발하려면 SDK가 필요합니다. 
Dart SDK를 직접 다운로드하거나(아래 설명 참조), 
전체 Dart SDK가 포함된 [Flutter SDK를 다운로드][download the Flutter SDK,]할 수 있습니다.

[download the Flutter SDK,]: {{site.flutter-docs}}/get-started/install

<ul class="tabs__top-bar">
  <li class="tab-link current" data-tab="tab-sdk-install-windows">Windows</li>
  <li class="tab-link" data-tab="tab-sdk-install-linux">Linux</li>
  <li class="tab-link" data-tab="tab-sdk-install-mac">Mac</li>
</ul>

<div id="tab-sdk-install-windows" class="tabs__content current">

  [Chocolatey](https://chocolatey.org)를 사용하여, Dart SDK의 stable 릴리스를 설치하세요.

  :::important
  이러한 명령에는 관리자 권한이 필요합니다. 
  관리자 레벨 명령 프롬프트를 시작하는 데 도움이 필요하면, 
  <em><a href="https://www.google.com/search?q=cmd+admin" target="blank">cmd admin</a>과 같은 검색을 시도하세요.</em>
  :::

  Dart SDK를 설치하려면:

  ```ps
  C:\> choco install dart-sdk
  ```

</div>

<div id="tab-sdk-install-linux" class="tabs__content">
  
  APT를 사용하여, Linux에 Dart SDK를 설치할 수 있습니다.

  1. 다음 일회성 셋업을 수행합니다.
  
     ```console
     $ sudo apt-get update
     $ sudo apt-get install apt-transport-https
     $ wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/dart.gpg
     $ echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' | sudo tee /etc/apt/sources.list.d/dart_stable.list
     ```

  2. Dart SDK 설치:
  
     ```console
     $ sudo apt-get update
     $ sudo apt-get install dart
     ```
     
</div>

<div id="tab-sdk-install-mac" class="tabs__content">

  [Homebrew](https://brew.sh/)를 사용하면, Dart를 쉽게 설치할 수 있습니다.

  ```console
  $ brew tap dart-lang/dart
  $ brew install dart
  ```

</div>

:::important
자세한 내용(**`PATH` 조정 방법** 포함)은, [Dart SDK 가져오기](/get-dart)를 참조하세요.
:::
