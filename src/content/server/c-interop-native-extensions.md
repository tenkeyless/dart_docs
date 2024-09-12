---
# title: Native extensions for the standalone Dart VM
title: 독립 실행형 Dart VM에 대한 네이티브 확장
# description: The original way for command-line Dart apps to call C/C++ functions.
description: 명령줄 Dart 앱이 C/C++ 함수를 호출하는 원래 방식입니다.
toc: false
---

:::note
이 페이지에서 이전에 논의했던 확장 메커니즘인 _네이티브 확장_ 은 Dart 2.15에서 제거되었습니다.

C 또는 C++로 작성된 기존 코드를 호출해야 하는 경우, [FFI 문서](/server/c-interop)를 참조하세요.

네이티브 확장과 유사한 메커니즘인 [Dart Embedding API][`include/dart_api.h`]는, 
Dart VM이 다른 애플리케이션에 라이브러리로 임베드될 때 지원됩니다. 
Dart Embedding API를 사용하는 방법에 대한 예는 [커뮤니티에서 유지 관리하는 이러한 예][examples]를 참조하세요.
:::

[`include/dart_api.h`]: {{site.repo.dart.sdk}}/blob/main/runtime/include/dart_api.h
[examples]: https://github.com/fuzzybinary/dart_shared_libray
