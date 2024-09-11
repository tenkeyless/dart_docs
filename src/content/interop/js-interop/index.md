---
# title: JavaScript interoperability
title: JavaScript 상호 운용성
# short-title: JS interop
short-title: JS 상호 운용성
# description: Integrate JavaScript code into your Dart web app.
description: Dart 웹앱에 JavaScript 코드를 통합합니다.
---

[Dart 웹 플랫폼](/overview#web-platform)은 `dart:js_interop`를 사용하여, 
JavaScript 앱 및 라이브러리와 브라우저 API와의 통신을 지원합니다.

웹 개발자는 Dart에서 아무것도 다시 작성하지 않고도, 
Dart 코드에서 외부 JS 라이브러리를 사용하여 이점을 얻을 수 있습니다.

## Next-generation JS interop {:#next-generation-js-interop}

The Dart team [recently][] overhauled the collection of features and APIs that allow
developers access to JavaScript and browser bindings in their Dart code.
This next generation of web interop not only improves user experience,
but also enables [Wasm][] support, aligning Dart with the future of the Web.

The following table maps Dart's new JS and web interop solutions to
their past counterparts:

| New interop libraries        | Previous libraries                       |
|------------------------------|------------------------------------------|
| [`package:web`][] | [`dart:html`][] <br> [`dart:indexed_db`][] <br> [`dart:svg`][] <br> [`dart:web_audio`][] <br> [`dart:web_gl`][] |
| [`dart:js_interop`][] <br> [`dart:js_interop_unsafe`][] | [`package:js`][] <br> [`dart:js`][] <br> [`dart:js_util`][] |

{:.table .table-striped}

The Dart interop story has been under heavy development for some time now;
check out the [Past JS interop][] page for a more in depth summary on past
iterations.

[recently]: https://medium.com/dartlang/dart-3-3-325bf2bf6c13
[Wasm]: /web/wasm
[`package:web`]: {{site.pub-pkg}}/web
[`dart:html`]: {{site.dart-api}}/dart-html/dart-html-library.html
[`dart:svg`]: {{site.dart-api}}/dart-svg/dart-svg-library.html
[`dart:indexed_db`]: {{site.dart-api}}/dart-indexed_db/dart-indexed_db-library.html
[`dart:web_audio`]: {{site.dart-api}}/dart-web_audio/dart-web_audio-library.html
[`dart:web_gl`]: {{site.dart-api}}/dart-web_gl/dart-web_gl-library.html
[`dart:js_interop`]: {{site.dart-api}}/dart-js_interop/dart-js_interop-library.html
[`dart:js_interop_unsafe`]: {{site.dart-api}}/dart-js_interop_unsafe/dart-js_interop_unsafe-library.html
[`package:js`]: {{site.pub-api}}/js
[`dart:js`]: {{site.dart-api}}/dart-js/dart-js-library.html
[`dart:js_util`]: {{site.dart-api}}/dart-js_util/dart-js_util-library.html
[Past JS interop]: /interop/js-interop/past-js-interop/

## Overview {:#overview}

For information on how to write and use JavaScript interop:
  * [Usage reference]
  * [JS types reference]

For information on interacting with web APIs:
  * [`package:web` and migration]

For tutorials and help:
  * [How to mock JavaScript interop objects]

For information on previous JavaScript interop libraries:
  * [Past JS interop]

For additional documentation on JavaScript interop:
  * [`dart:js_interop` API reference]
  * [`dart:js_interop_unsafe` API reference]

[Usage reference]: /interop/js-interop/usage
[JS types reference]: /interop/js-interop/js-types
[`package:web` and migration]: /interop/js-interop/package-web
[How to mock JavaScript interop objects]: /interop/js-interop/mock
[Past JS interop]: /interop/js-interop/past-js-interop
[`dart:js_interop` API reference]: {{site.dart-api}}/dart-js_interop/dart-js_interop-library.html
[`dart:js_interop_unsafe` API reference]: {{site.dart-api}}/dart-js_interop_unsafe/dart-js_interop_unsafe-library.html
