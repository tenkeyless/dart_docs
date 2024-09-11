---
# title: Security advisories
title: 보안 권고
# description: >-
#   Use security advisories to inform and be informed
#   about security vulnerabilities in Dart packages.
description: >-
  보안 권고를 활용하여 Dart 패키지의 보안 취약성에 대한 정보를 얻고 이를 공유하세요.
---

보안 권고는 보안 취약성에 대한 정보를 보고하는 수단입니다. 
Pub은 Dart 및 Flutter 패키지에 대한 보안 권고를 게시하기 위해, 
[GitHub 권고 데이터베이스][GitHub Advisory Database]를 사용합니다.

GitHub 저장소에 권고를 만들려면, 
GitHub의 [저장소 보안 권고 생성][Creating a repository security advisory]에 대한 문서에서 설명한 대로, 
GitHub의 보안 권고 보고 메커니즘을 사용합니다. 
먼저 초안 보안 권고를 만들면, GitHub에서 검토하여 중앙 권고 데이터베이스에 수집합니다.

[GitHub Advisory Database]: https://github.com/advisories
[Creating a repository security advisory]: https://docs.github.com/code-security/security-advisories/working-with-repository-security-advisories/creating-a-repository-security-advisory

## Pub 클라이언트의 보안 권고 사항 {:#security-advisories-in-the-pub-client}

pub 클라이언트는 종속성 해결(resolution) 시 보안 권고를 표면화합니다. 
예를 들어, `dart pub get`을 실행하면 다음과 같은 출력이 표시됩니다.

```console
$ dart pub get
Resolving dependencies...
http 0.13.0 (affected by advisory: [^0], 1.2.0 available)
Got dependencies!
Dependencies are affected by security advisories:
  [^0]: https://github.com/advisories/GHSA-4rgh-jx4f-qfcq
```

해결(resolution)에서 권고 사항을 식별하는 경우, 
Dart 팀은 링크를 방문하여 권고 사항을 검토할 것을 권장합니다. 
취약성이 패키지에 영향을 미친다고 판단되는 경우, 
영향을 받지 않는 종속성 버전으로 업그레이드하는 것을 강력히 고려해야 합니다.


### 보안 권고 무시 {:#ignoring-security-advisories}

보안 권고가 애플리케이션과 관련이 없는 경우, 
패키지의 `pubspec.yaml`에 있는 [`ignored_advisories`][] 리스트에 권고 식별자를 추가하여, 
경고를 억제할 수 있습니다. 
예를 들어, 다음은 GHSA 식별자가 `GHSA-4rgh-jx4f-qfcq`인 권고를 무시합니다.

```yaml
name: myapp
dependencies:
  foo: ^1.0.0
ignored_advisories:
 - GHSA-4rgh-jx4f-qfcq
```

`ignored_advisories` 리스트는 루트 패키지에만 영향을 미칩니다. 
종속성에서 무시된 권고 사항은 자신의 패키지에 대한 패키지 해결에 영향을 미치지 않습니다.

[`ignored_advisories`]: /tools/pub/pubspec#ignored_advisories