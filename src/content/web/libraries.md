---
# title: Web libraries and packages
title: 웹 라이브러리 및 패키지
# short-title: Web libraries
short-title: 웹 라이브러리
# description: Libraries and packages that can help you write Dart web apps.
description: Dart 웹앱을 작성하는 데 도움이 되는 라이브러리와 패키지입니다.
---

Dart는 웹 앱 개발을 지원하기 위해 여러 패키지와 라이브러리를 제공하며, 
권장 옵션은 [`package:web`][web]입니다. 
[Dart SDK][]에는 낮은 레벨 웹 API를 제공하는 다른 라이브러리도 포함되어 있습니다.

## 웹 솔루션 {:#web-solutions}

[`package:web`으로 마이그레이션][migrate]
: ([`dart:html`][html]과 같은) Dart의 이전 웹 라이브러리 솔루션에서, 
  `package:web`으로 마이그레이션하는 방법을 알아보세요.

[`package:web` API 참조][web]
: Dart의 권장 웹 상호 운용(interop) 솔루션 `package:web`은 
  정적 JS 상호 운용을 중심으로 구축된 가벼운 바인딩으로 브라우저 API를 노출합니다.

[JavaScript 상호 운용성 문서][js]
: Dart의 JS 상호 운용 지원을 사용하여, 
  기존 JavaScript 또는 TypeScript 라이브러리와 상호 작용하는 방법을 알아보세요.

[`dart:js_interop` API 참조][js_interop]
: Dart의 웹 라이브러리 `dart:js_interop`는 JavaScript와 Dart 타입 간의 
  건전한 상호 운용을 용이하게 하는 데 필요한 모든 멤버를 제공합니다.

[Flutter 웹 지원][flutter-web]
: [Flutter 프레임워크][flutter]는 모바일, 데스크톱 및 임베디드 장치 지원 외에도, 
  Dart를 사용한 웹 개발을 지원합니다.

[Dart로 웹 앱 빌드](/web/get-started)
: Dart로 웹 앱을 빌드, 실행 및 디버깅하는 방법에 대한 간략한 개요입니다.

웹 플랫폼을 지원하는 다른 라이브러리를 찾으려면, pub.dev에서 [웹 패키지][web packages]를 검색하세요.

[web]: {{site.pub-pkg}}/web
[Dart SDK]: {{site.dart-api}}/{{site.sdkInfo.channel}}
[migrate]: /interop/js-interop/package-web
[js_interop]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-js_interop/dart-js_interop-library.html
[flutter-web]: {{site.flutter-docs}}/platform-integration/web
[flutter]: {{site.flutter}}
[web packages]: {{site.pub}}/web
[html]: /libraries/dart-html
[js]: /interop/js-interop