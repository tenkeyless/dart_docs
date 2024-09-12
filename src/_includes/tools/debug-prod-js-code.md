## 프로덕션 코드 디버깅 {:#debugging}

이 섹션에서는 Chrome, Firefox 및 Safari에서 프로덕션 컴파일된 코드를 디버깅하기 위한 팁을 제공합니다. 
Chrome과 같이 소스 맵을 지원하는 브라우저에서만 JavaScript 코드를 디버깅할 수 있습니다.

:::tip
가능하다면 프로덕션 코드를 디버깅하는 대신, 
[`webdev`][]에서 제공하는 개발 서버를 사용하여 코드를 디버깅하세요.
:::

[`webdev`]: /tools/webdev

어떤 브라우저를 사용하든, 적어도 잡히지 않은 예외(uncaught exceptions)에서 일시 중지를 활성화해야 하며, 
아마도 모든 예외에서 일시 중지를 활성화해야 합니다. 
try-catch로 사용자 코드를 래핑하는 `dart:async`와 같은 프레임워크의 경우, 
모든 예외에서 일시 중지를 권장합니다.

[debugging web apps]: /web/debugging


### Chrome {:#dart2js-debugging-chrome}

Chrome에서 디버깅하려면:

1. [Chrome DevTools 문서](https://developer.chrome.com/docs/devtools/)에 설명된 대로 
   개발자 도구(Developer Tools) 창을 엽니다.
2. [Chrome의 SourceMaps](https://bit.ly/YugIUY) 비디오에 설명된 대로, 소스 맵을 켭니다.
3. [중단점 설정 방법](https://developer.chrome.com/docs/devtools/javascript/breakpoints/)에 설명된 대로, 
   모든 예외에서 또는 처리되지 않은 예외(uncaught exceptions)에서만 디버깅을 활성화합니다.
4. 앱을 다시 로드합니다.

### Edge {:#dart2js-debugging-ie}

Edge에서 디버깅하려면:

1. Edge의 최신 버전으로 업데이트합니다.
2. **개발자 도구(Developer Tools)**를 로드합니다. (**[F12](https://docs.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/landing/)**)
3. 앱을 다시 로드합니다. **debugger** 탭에는 소스 매핑 파일이 표시됩니다.
4. **Ctrl+Shift+E**를 통해 예외 동작을 제어할 수 있습니다. 기본값은 **Break on unhandled exceptions**입니다.

### Firefox {:#dart2js-debugging-firefox}

Firefox에서 디버깅하려면:

1. [Firefox 개발자 도구 문서](https://firefox-source-docs.mozilla.org/devtools-user/index.html)에 설명된 대로 **Web Developer Tools** 창을 엽니다.

2. 다음 그림과 같이 **Pause on exceptions**를 활성화합니다.

   <img width="640px" src="/assets/img/ff-debug.png" alt="Firefox 디버거에서 예외 시 일시 중지 활성화">

3. 앱을 다시 로드합니다. **Debugger** 탭에는 소스 매핑 파일이 표시됩니다.

### Safari {:#dart2js-debugging-safari}

Safari에서 디버깅하려면:

1. [Safari Web Inspector 튜토리얼]({{site.apple-dev}}/library/archive/documentation/NetworkingInternetWeb/Conceptual/Web_Inspector_Tutorial/EnableWebInspector/EnableWebInspector.html)에 설명된 대로 **Develop** 메뉴를 켭니다.
2. 모든 예외에 대해 중단을 활성화하거나, 처리되지 않은 예외에 대해서만 중단을 활성화합니다. [Safari Developer Help](https://support.apple.com/en-ca/guide/safari-developer/add-a-javascript-breakpoint-dev5e4caf347/mac)에서 [JavaScript 중단점 추가](https://support.apple.com/en-ca/guide/safari-developer/welcome/mac)를 참조하세요.
3. 앱을 다시 로드합니다.

