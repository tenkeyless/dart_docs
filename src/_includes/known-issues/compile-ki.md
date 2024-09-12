크로스 컴파일 지원 없음([문제 28617][issue 28617])
: 컴파일러는 컴파일하는 운영 체제에 대한 머신 코드만 생성할 수 있습니다. 
  macOS, Windows, Linux용 실행 파일을 생성하려면, 컴파일러를 세 번 실행해야 합니다. 
  세 운영 체제를 모두 지원하는 CI(Continuous Integration) 공급자를 사용할 수도 있습니다.

`dart:mirrors` 및 `dart:developer` 지원 없음
: 사용할 수 있는 코어 라이브러리의 전체 리스트는, 
  [멀티 플랫폼][Multi-platform] 및 [네이티브 플랫폼][Native platform] 라이브러리 표를 참조하세요.

[Multi-platform]: /libraries#multi-platform-libraries
[Native platform]: /libraries#native-platform-libraries
[issue 28617]: {{site.repo.dart.sdk}}/issues/28617

:::tip
이러한 문제 중 하나가 당신에게 중요하다면, 해당 문제에 "좋아요"를 눌러 Dart 팀에 알려주세요.
:::
