---
# title: Web deployment
title: 웹 배포
# description: Learn how to build your Dart web app for production deployment.
description: 프로덕션 배포를 위한 Dart 웹 앱을 만드는 방법을 알아보세요.
---

Dart 웹 앱 배포는 다른 웹 앱 배포와 동일하게 작동합니다. 
이 페이지에서는 앱을 컴파일하는 방법, 
앱을 더 작고 빠르게 만드는 팁, 앱을 서비스하기 위한 리소스를 설명합니다.

## 앱 구축하기 {:#compiling-to-javascript}

`webdev` 도구를 사용하여 앱을 빌드하세요. 
이 도구는 Dart를 JavaScript로 컴파일하고, 배포에 필요한 모든 에셋을 생성합니다. 
컴파일러의 프로덕션 모드를 사용하여 빌드하면, 컴파일러가 트리 셰이킹을 지원하여, 
상당히 작은 JavaScript 파일을 얻을 수 있습니다.

약간의 추가 작업으로, 배포 가능한 앱을 [더 작고, 더 빠르고, 더 안정적으로](#make-your-app-smaller-faster-and-more-reliable) 만들 수 있습니다.

### Webdev를 사용하여 컴파일 {:#compile-using-webdev}

[`webdev build` 명령을 사용하여][build], 배포 가능한 버전의 앱을 만듭니다. 
이 명령은 코드를 JavaScript로 변환하고, 결과를 `build/web/main.dart.js`로 저장합니다. 
`webdev build`와 함께 [`dart compile js`에 사용 가능한 모든 옵션](/tools/dart-compile#prod-compile-options)을 사용할 수 있습니다.

### 앱을 더 작고, 더 빠르고, 더 안정적으로 만들어보세요 {:#make-your-app-smaller-faster-and-more-reliable}

다음 단계는 선택 사항입니다. 앱의 안정성과 반응성을 높이는 데 도움이 될 수 있습니다.

* [지연된(deferred) 로딩을 사용하여, 앱의 초기 크기를 줄이기](#use-deferred-loading-to-reduce-your-apps-initial-size)
* [웹 앱에 대한 모범 사례 따르기](#follow-best-practices-for-web-apps)
* [불필요한 빌드 파일 제거하기](#remove-unneeded-build-files)

#### 지연된 로딩을 사용하여 앱의 초기 크기를 줄이세요. {:#use-deferred-loading-to-reduce-your-apps-initial-size}

Dart의 지연 로딩 지원을 사용하면 앱의 초기 다운로드 크기를 줄일 수 있습니다. 
자세한 내용은 언어 투어의 [지연 로딩](/language/libraries#lazily-loading-a-library)에 대한 내용을 참조하세요.

#### 웹 앱에 대한 모범 사례를 따르세요. {:#follow-best-practices-for-web-apps}

웹 앱에 대한 일반적인 조언은 Dart 웹 앱에도 적용됩니다. 다음은 몇 가지 리소스입니다.

* [빠른 로드 시간](https://web.dev/fast/)
* [웹 기초](https://developers.google.com/web/fundamentals/) (특히 [콘텐츠 효율성 최적화](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/))
* [프로그레시브 웹 앱](https://web.dev/progressive-web-apps/)
* [Lighthouse](https://developers.google.com/web/tools/lighthouse/)

#### 불필요한 빌드 파일을 제거하세요. {:#remove-unneeded-build-files}

웹 컴파일러는 개발 중에는 유용하지만 프로덕션에서는 불필요한, 
Dart-to-JavaScript 맵 파일과 같은 파일을 생성할 수 있습니다.

이러한 파일을 제거하려면, 다음과 같은 명령을 실행할 수 있습니다.

{% comment %}
Revise the following once https://github.com/dart-lang/angular/issues/1123 is resolved:
{% endcomment %}

```console
# From the root directory of your app:
$ find build -type f -name "*.js.map" -exec rm {} +
```

## 앱 서비스하기 {:#serving-your-app}

다른 웹 앱을 서비스하는 것처럼, Dart 웹 앱을 서비스할 수 있습니다. 
이 섹션에서는 Dart 웹 앱을 서비스하기 위한 팁과, 
GitHub Pages 또는 Firebase를 사용하여 앱을 서비스하는 데 도움이 되는, 
Dart 관련 리소스를 설명합니다.

### GitHub Pages {:#github-pages}

앱에서 라우팅을 사용하지 않거나 서버 측 지원이 필요하지 않은 경우, 
[GitHub Pages](https://pages.github.com/)를 사용하여 앱을 제공할 수 있습니다. 
[peanut][] 패키지는 모든 Dart 웹 앱에 대한 gh-pages 브랜치를 자동으로 생성하는 간편한 방법입니다.

[startup_namer 예제](https://filiph.github.io/startup_namer/)는 GitHub Pages를 사용하여 호스팅됩니다. 
해당 파일은 [filiph/startup_namer repo](https://github.com/filiph/startup_namer)의 **gh-pages** 브랜치에 있으며, [peanut][]을 사용하여 빌드되었습니다.

### Firebase {:#firebase}
{% comment %}
TODO: Give an example of how to deploy with Firebase, which originally was shown on https://dart.academy/build-a-real-time-chat-web-app-with-dart-angular-2-and-firebase-3/
{% endcomment %}

Firebase를 사용하여 배포하는 방법에 대해 자세히 알아보려면, 다음 리소스를 참조하세요.

* [Firebase 호스팅 문서](https://firebase.google.com/docs/hosting/)는 
  Firebase를 사용하여 웹 앱을 배포하는 방법을 설명합니다.
* Firebase 호스팅 문서의 [호스팅 동작 구성](https://firebase.google.com/docs/hosting/full-config)은 리디렉션, 재작성 등을 다룹니다.

[build]: /tools/webdev#build
[build_runner]: /tools/build_runner
[build_web_compilers]: {{site.pub-pkg}}/build_web_compilers
[config]: /tools/build_runner#config
[peanut]: {{site.pub-pkg}}/peanut
[webdev]: /tools/webdev
