---
title: dart:html
# description: Learn about the major features in Dart's dart:html library.
description: Dart의 dart:html 라이브러리의 주요 기능에 대해 알아보세요.
prevpage:
  url: /libraries/dart-io
  title: dart:io
---

:::warning
`dart:html`은 [`package:web`][]으로 대체됩니다. 
패키지 관리자는 Wasm과 호환되도록, 가능한 한 빨리 `package:web`으로 마이그레이션해야 합니다. 
지침은 [package:web으로 마이그레이션][Migrate to package:web] 페이지를 읽어보세요.
:::

[dart:html][] 라이브러리를 사용하여 브라우저를 프로그래밍하고, 
DOM에서 객체와 요소를 조작하고, HTML5 API에 액세스합니다. 
DOM은 HTML 페이지의 계층 구조를 설명하는 *Document Object Model*의 약자입니다.

dart:html의 다른 일반적인 사용 사례로는 스타일(*CSS*) 조작, 
HTTP 요청을 사용하여 데이터 가져오기, 
[WebSockets](#sending-and-receiving-real-time-data-with-websockets)를 사용하여 데이터 교환 등이 있습니다. 
HTML5(및 dart:html)에는 이 섹션에서 다루지 않는 추가 API가 많이 있습니다. 
웹 앱만 dart:html을 사용할 수 있으며, 명령줄 앱은 사용할 수 없습니다.

:::note
대규모 애플리케이션의 경우 또는 이미 Flutter 애플리케이션이 있는 경우, 
[웹용 Flutter]({{site.flutter}}/web)를 사용하는 것을 고려하세요.
:::

웹 앱에서 HTML 라이브러리를 사용하려면, dart:html을 import 합니다.

<?code-excerpt "html/lib/html.dart (import)"?>
```dart
import 'dart:html';
```

[`package:web`]: {{site.pub-pkg}}/web
[Migrate to package:web]: /interop/js-interop/package-web

## DOM 조작하기 {:#manipulating-the-dom}

DOM을 사용하려면, *windows*, *documents*, *elements*, *nodes*에 대해 알아야 합니다.

[Window][] 객체는 웹 브라우저의 실제 창을 나타냅니다. 
각 Window에는 현재 로드된 문서를 가리키는 Document 객체가 있습니다. 
Window 객체에는 또한 IndexedDB(데이터 저장용), requestAnimationFrame(애니메이션용) 등과 같은 다양한 API에 대한 접근자가 있습니다. 
탭 브라우저에서, 각 탭에는 고유한 Window 객체가 있습니다.

[Document][] 객체를 사용하면, 
문서 내에서 [Element][] 객체를 만들고 조작할 수 있습니다. 
문서 자체가 요소이며, 조작할 수 있다는 점에 유의하세요.

DOM은 [Nodes][]의 트리를 모델링합니다. 
이러한 노드는 종종 요소이지만, 속성, 텍스트, 주석 및 기타 DOM 타입일 수도 있습니다. 
부모가 없는 루트 노드를 제외하고, DOM의 각 노드에는 부모가 하나 있고 자식이 여러 개 있을 수 있습니다.

### 요소 찾기 {:#finding-elements}

요소를 조작하려면, 먼저 해당 요소를 나타내는 객체가 필요합니다. 
쿼리를 사용하여 이 객체를 가져올 수 있습니다.

최상위 함수 `querySelector()` 및 `querySelectorAll()`를 사용하여 하나 이상의 요소를 찾습니다. 
ID, 클래스, 태그, 이름 또는 이러한 조합으로 쿼리할 수 있습니다. 
[CSS 선택기 사양 가이드](https://www.w3.org/TR/css3-selectors/)는 
ID를 지정하기 위한 \# 접두사와 클래스의 마침표(.)를 사용하는 것과 같이 선택기의 형식을 정의합니다.

`querySelector()` 함수는 선택기와 일치하는 첫 번째 요소를 반환하는 반면, 
`querySelectorAll()`은 선택기와 일치하는 요소의 컬렉션들을 반환합니다.

<?code-excerpt "html/lib/html.dart (query-selector)"?>
```dart
// id(an-id)로 요소를 찾습니다.
Element idElement = querySelector('#an-id')!;

// 클래스(a-class)로 요소를 찾습니다.
Element classElement = querySelector('.a-class')!;

// 태그(<div>)로 모든 요소를 ​​찾으세요.
List<Element> divElements = querySelectorAll('div');

// 모든 텍스트 입력을 찾습니다.
List<Element> textInputElements = querySelectorAll(
  'input[type="text"]',
);

// ID가 'id'인 요소 내부에 있는, 
// <p> 내에서 CSS 클래스가 'class'인 모든 요소를 ​​찾습니다.
List<Element> specialParagraphElements = querySelectorAll('#id p.class');
```

### 요소 조작하기 {:#manipulating-elements}

속성을 사용하여 요소의 상태를 변경할 수 있습니다. 
Node와 하위 타입 Element는 모든 요소가 갖는 속성을 정의합니다. 
예를 들어, 모든 요소에는 상태를 설정하는 데 사용할 수 있는 `classes`, `hidden`, `id`, `style` 및 `title` 속성이 있습니다. 
Element의 하위 클래스는 ([AnchorElement][]의 `href` 속성과 같은) 추가적인 속성을 정의합니다.

HTML에서 앵커 요소를 지정하는 다음 예를 고려하세요.

<?code-excerpt "html/test/html_test.dart (anchor-html)" replace="/.*'(.*?)'.*/$1/g"?>
```html
<a id="example" href="/another/example">link text</a>
```

이 `<a>` 태그는, 
`href` 속성과 문자열 "link text"를 포함하는 텍스트 노드(`text` 속성을 통해 액세스 가능)가 있는 요소를 지정합니다. 
링크가 이동하는 URL을 변경하려면, AnchorElement의 `href` 속성을 사용할 수 있습니다.

<?code-excerpt "html/test/html_test.dart (href)" plaster="none"?>
```dart
var anchor = querySelector('#example') as AnchorElement;
anchor.href = 'https://dart.dev';
```

종종 여러 요소에 속성을 설정해야 합니다. 
예를 들어, 다음 코드는 "mac", "win" 또는 "linux" 클래스를 가진, 
모든 요소의 `hidden` 속성을 설정합니다. 
`hidden` 속성을 true로 설정하는 것은, 
CSS에 `display: none`을 추가하는 것과 같은 효과가 있습니다.

<?code-excerpt "html/test/html_test.dart (os-html)" replace="/.*? = '''|''';$//g"?>
```html
<!-- HTML 에서: -->
<p>
  <span class="linux">Words for Linux</span>
  <span class="macos">Words for Mac</span>
  <span class="windows">Words for Windows</span>
</p>
```

<?code-excerpt "html/test/html_test.dart (os)"?>
```dart
// Dart 에서:
const osList = ['macos', 'windows', 'linux'];
final userOs = determineUserOs();

// 가능한 각 OS에 대해...
for (final os in osList) {
  // 사용자 OS와 일치하나요?
  bool shouldShow = (os == userOs);

  // class=os인 모든 요소를 ​​찾습니다. 
  // 예를 들어, os == 'windows'인 경우, 
  // querySelectorAll('.windows')를 호출하여, 
  // 클래스가 "windows"인 모든 요소를 ​​찾습니다. 
  // '.$os'는 문자열 보간을 사용합니다.
  for (final elem in querySelectorAll('.$os')) {
    elem.hidden = !shouldShow; // 표시하거나 숨기세요.
  }
}
```

적절한 속성을 사용할 수 없거나 편리하지 않은 경우, 
Element의 `attributes` 속성을 사용할 수 있습니다. 
이 속성은 키가 속성 이름인 `Map<String, String>`입니다. 
속성 이름과 의미 리스트는 [MDN Attributes 페이지](https://developer.mozilla.org/docs/Web/HTML/Attributes)를 참조하세요. 
다음은 속성 값을 설정하는 예입니다.

<?code-excerpt "html/lib/html.dart (attributes)"?>
```dart
elem.attributes['someAttribute'] = 'someValue';
```

### 요소 생성 {:#creating-elements}

새 요소를 만들고 DOM에 첨부하여, 기존 HTML 페이지에 추가할 수 있습니다. 
다음은 문단(\<p\>) 요소를 만드는 예입니다.

<?code-excerpt "html/lib/html.dart (creating-elements)"?>
```dart
var elem = ParagraphElement();
elem.text = 'Creating is easy!';
```

HTML 텍스트를 파싱하여 요소를 만들 수도 있습니다. 어떤 자식 요소든 파싱되어 만들어집니다.

<?code-excerpt "html/lib/html.dart (creating-from-html)"?>
```dart
var elem2 = Element.html(
  '<p>Creating <em>is</em> easy!</p>',
);
```

앞의 예에서, `elem2`는 `ParagraphElement`입니다.

요소에 부모를 지정하여 새로 만든 요소를 ​​문서에 첨부합니다. 
기존 요소의 자식에 요소를 추가할 수 있습니다. 
다음 예에서, `body`는 요소이고, 
그것의 자식 요소는 `children` 속성에서 (`List<Element>`로서) 액세스할 수 있습니다.

<?code-excerpt "html/lib/html.dart (body-children-add)"?>
```dart
document.body!.children.add(elem2);
```

### 노드 추가, 교체 및 제거 {:#adding-replacing-and-removing-nodes}

요소는 노드의 일종일 뿐이라는 점을 기억하세요. 
노드의 `nodes` 속성을 사용하여, 노드의 모든 자식을 찾을 수 있습니다. 
이 속성은 `List<Node>`를 반환합니다. (`children`은 Element가 아닌 노드를 생략합니다)
이 리스트가 있으면, 일반적인 List 메서드와 연산자를 사용하여, 노드의 자식을 조작할 수 있습니다.

노드를 부모의 마지막 자식으로 추가하려면, 리스트의 `add()` 메서드를 사용합니다.

<?code-excerpt "html/lib/html.dart (nodes-add)"?>
```dart
querySelector('#inputs')!.nodes.add(elem);
```

노드를 바꾸려면, Node의 `replaceWith()` 메서드를 사용하세요.

<?code-excerpt "html/lib/html.dart (replace-with)"?>
```dart
querySelector('#status')!.replaceWith(elem);
```

노드를 제거하려면, Node의 `remove()` 메서드를 사용합니다.

<?code-excerpt "html/lib/html.dart (remove)"?>
```dart
// ID로 노드를 찾고, 해당 노드가 있으면 DOM에서 제거합니다.
querySelector('#expendable')?.remove();
```

### CSS 스타일 조작 {:#manipulating-css-styles}

CSS 또는 *계단식 스타일 시트*는 DOM 요소의 표현 스타일을 정의합니다. 
ID 및 클래스 속성을 첨부하여, 요소의 모양을 변경할 수 있습니다.

각 요소에는 리스트인 `classes` 필드가 있습니다. 
이 컬렉션에서 문자열을 추가하고 제거하는 것만으로, CSS 클래스를 추가하고 제거할 수 있습니다. 
예를 들어, 다음 샘플은 요소에 `warning` 클래스를 추가합니다.

<?code-excerpt "html/lib/html.dart (classes-add)"?>
```dart
var elem = querySelector('#message')!;
elem.classes.add('warning');
```

ID로 요소를 찾는 것이 종종 매우 효율적입니다. 
`id` 속성을 사용하여 요소 ID를 동적으로 설정할 수 있습니다.

<?code-excerpt "html/lib/html.dart (set-id)"?>
```dart
var message = DivElement();
message.id = 'message2';
message.text = 'Please subscribe to the Dart mailing list.';
```

이 예제에서 중복된 텍스트를 줄이려면, 메서드 cascades를 사용하면 됩니다.

<?code-excerpt "html/lib/html.dart (elem-set-cascade)"?>
```dart
var message = DivElement()
  ..id = 'message2'
  ..text = 'Please subscribe to the Dart mailing list.';
```

ID와 클래스를 사용하여 요소를 스타일 세트와 연결하는 것이 가장 좋은 방법이지만, 
때로는 특정 스타일을 요소에 직접 연결하고 싶을 수도 있습니다.

<?code-excerpt "html/lib/html.dart (set-style)"?>
```dart
message.style
  ..fontWeight = 'bold'
  ..fontSize = '3em';
```

### 이벤트 처리 {:#handling-events}

클릭, 초점 변경, 선택과 같은 외부 이벤트에 응답하려면, 이벤트 리스너를 추가합니다. 
페이지의 모든 요소에 이벤트 리스너를 추가할 수 있습니다. 
이벤트 전달 및 전파는 복잡한 주제입니다. 
웹 프로그래밍을 처음 접한다면 [자세한 내용을 조사하세요](https://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture).

<code><em>element</em>.on<em>Event</em>.listen(<em>function</em>)</code>를 사용하여, 
이벤트 핸들러를 추가합니다. 
여기서 <code><em>Event</em></code>는 이벤트 이름이고, 
<code><em>function</em></code>은 이벤트 핸들러입니다.

예를 들어, 버튼 클릭을 처리하는 방법은 다음과 같습니다.

<?code-excerpt "html/lib/html.dart (on-click)"?>
```dart
// ID로 버튼을 찾고 이벤트 핸들러를 추가합니다.
querySelector('#submitInfo')!.onClick.listen((e) {
  // 버튼을 클릭하면, 이 코드가 실행됩니다.
  submitData();
});
```

이벤트는 DOM 트리를 통해 위아래로 전파될 수 있습니다. 
원래 이벤트를 발생시킨 요소를 찾으려면, `e.target`을 사용합니다.

<?code-excerpt "html/lib/html.dart (target)"?>
```dart
document.body!.onClick.listen((e) {
  final clickedElem = e.target;
  // ...
});
```

이벤트 리스너를 등록할 수 있는 모든 이벤트를 보려면, 
[Element][] 및 하위 클래스의 API 문서에서 "onEventType" 속성을 찾으세요. 
일반적인 이벤트는 다음과 같습니다.

-   change
-   blur
-   keyDown
-   keyUp
-   mouseDown
-   mouseUp


## HttpRequest로 HTTP 리소스 사용 {:#using-http-resources-with-httprequest}

`dart:html`을 직접 사용하여 HTTP 요청을 하는 것은 피해야 합니다. 
`dart:html`의 [`HttpRequest`][] 클래스는 플랫폼에 따라 다르며, 단일 구현에 연결되어 있습니다. 
대신, [`package:http`]({{site.pub-pkg}}/http)와 같은 높은 레벨 라이브러리를 사용하세요.

[인터넷에서 데이터 가져오기][Fetch data from the internet] 튜토리얼은 `package:http`를 사용하여, 
HTTP 요청을 하는 방법을 설명합니다.

## WebSockets으로 실시간 데이터 송수신 {:#sending-and-receiving-real-time-data-with-websockets}

WebSocket을 사용하면 웹 앱이 서버와 상호작용으로 데이터를 교환할 수 있습니다. 
폴링(polling)이 필요하지 않습니다. 
서버는 WebSocket을 만들고, **ws://**로 시작하는 URL에서 요청을 수신합니다. 
예를 들어 ws://127.0.0.1:1337/ws입니다. 
WebSocket을 통해 전송되는 데이터는 문자열 또는 blob일 수 있습니다. 
종종, 데이터는 JSON 형식의 문자열입니다.

웹 앱에서 WebSocket을 사용하려면, 
먼저 [WebSocket][] 객체를 만들고, 
WebSocket URL을 인수로 전달합니다.

{% comment %}
Code inspired by:
https://github.com/dart-lang/dart-samples/blob/master/html5/web/websockets/basics/websocket_sample.dart

Once tests are written for the samples, consider getting code excerpts from
the websocket sample app.
{% endcomment %}

<?code-excerpt "html/test/html_test.dart (WebSocket)"?>
```dart
var ws = WebSocket('ws://echo.websocket.org');
```

### 데이터 전송 {:#sending-data}

WebSocket에서 문자열 데이터를 보내려면, `send()` 메서드를 사용하세요.

<?code-excerpt "html/test/html_test.dart (send)"?>
```dart
ws.send('Hello from Dart!');
```

### 데이터 수신 {:#receiving-data}

WebSocket에서 데이터를 수신하려면, 메시지 이벤트에 대한 리스너를 등록하세요.

<?code-excerpt "html/test/html_test.dart (onMessage)" plaster="none"?>
```dart
ws.onMessage.listen((MessageEvent e) {
  print('Received message: ${e.data}');
});
```

메시지 이벤트 핸들러는 [MessageEvent][] 객체를 수신합니다. 
이 객체의 `data` 필드에는 서버의 데이터가 있습니다.

### WebSocket 이벤트 처리 {:#handling-websocket-events}

앱은 다음 WebSocket 이벤트를 처리할 수 있습니다: open, close, error, (앞서 보여준 대로) message. 
다음은 WebSocket 객체를 생성하고, 
open, close, error, message 이벤트에 대한 핸들러를 등록하는 메서드의 예입니다:

<?code-excerpt "html/test/html_test.dart (initWebSocket)" plaster="none"?>
```dart
void initWebSocket([int retrySeconds = 1]) {
  var reconnectScheduled = false;

  print('Connecting to websocket');

  void scheduleReconnect() {
    if (!reconnectScheduled) {
      Timer(Duration(seconds: retrySeconds),
          () => initWebSocket(retrySeconds * 2));
    }
    reconnectScheduled = true;
  }

  ws.onOpen.listen((e) {
    print('Connected');
    ws.send('Hello from Dart!');
  });

  ws.onClose.listen((e) {
    print('Websocket closed, retrying in $retrySeconds seconds');
    scheduleReconnect();
  });

  ws.onError.listen((e) {
    print('Error connecting to ws');
    scheduleReconnect();
  });

  ws.onMessage.listen((MessageEvent e) {
    print('Received message: ${e.data}');
  });
}
```


## 더 많은 정보 {:#more-information}

이 섹션은 dart:html 라이브러리 사용의 표면을 간신히 긁어낸 것입니다. 
자세한 내용은, [dart:html][]에 대한 문서를 참조하세요. 
Dart에는 ([웹 오디오][web audio] [IndexedDB][] 및 [WebGL][]과 같은) 
보다 특수화된 웹 API를 위한 추가 라이브러리가 있습니다.

Dart 웹 라이브러리에 대한 자세한 내용은, [웹 라이브러리 개요][web library overview]를 참조하세요.

[AnchorElement]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/AnchorElement-class.html
[dart:html]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/dart-html-library.html
[Fetch data from the internet]: /tutorials/server/fetch-data
[Document]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/Document-class.html
[Element]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/Element-class.html
[`HttpRequest`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/HttpRequest-class.html
[IndexedDB]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-indexed_db/dart-indexed_db-library.html
[MessageEvent]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/MessageEvent-class.html
[Nodes]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/Node-class.html
[web audio]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-web_audio/dart-web_audio-library.html
[WebGL]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-web_gl/dart-web_gl-library.html
[WebSocket]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/WebSocket-class.html
[web library overview]: /web/libraries
[Window]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/Window-class.html
