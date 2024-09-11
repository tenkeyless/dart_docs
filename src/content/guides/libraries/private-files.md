---
# title: What not to commit
title: 커밋하지 말아야 할 것
# description: Your development tools generate a bunch of files. Not all of them should be committed.
description: 개발 도구는 많은 파일을 생성합니다. 모든 파일을 커밋할 필요는 없습니다.
---

[pub 도구](/tools/pub/cmd), [GitHub](https://github.com/) 또는 다른 소스 코드 관리 시스템을 사용하여 Dart 소스 코드를 저장소에 넣을 때, 
IDE나 코드 편집기, pub 도구 및 기타 도구에서 생성하는 대부분의 파일을 포함하지는 마세요.

:::note
별도로 언급된 경우를 제외하고, 이 페이지에서는 소스 코드 저장소만 논의하며, 앱 배포는 _아닙니다_. 
일반적으로 저장소에 넣지 않는 일부 파일은 앱을 배포할 때 유용하거나 필수적입니다.
:::

## 규칙 {:#the-rules}

pub에서 생성한 다음 파일과 디렉토리를 **커밋하지 마세요**.

```plaintext
.dart_tool/
build/
pubspec.lock  # 애플리케이션 패키지를 제외하고
```

[`dart doc`](/tools/dart-doc)에서 생성된 API 문서 디렉토리를 **커밋하지 마세요**:

```plaintext
doc/api/
```

다른 개발 환경에서 만든 파일과 디렉토리를 **커밋하지 마세요**. 

예를 들어, 개발 환경에서 다음 파일을 만드는 경우, 글로벌 ignore 파일에 넣는 것을 고려하세요.

```plaintext
# IntelliJ
*.iml
*.ipr
*.iws
.idea/

# Mac
.DS_Store
```

더 자세한 내용을 읽어보세요.

## 세부사항 {:#details}

원칙적으로, 사람들이 당신의 패키지 또는 소스 코드 저장소를 사용하는 데 필요한 파일만 커밋합니다. 
추가 파일을 포함하는 것은 불필요하고, 역효과가 있을 수 있으며, 
머신 설정에 대한 세부 정보를 노출하는 경우 보안에 영향을 미칠 수 있습니다. 
많은 소스 코드 저장소에서 일반적인 관행은, 생성된 파일을 전혀 커밋하지 않는 것입니다.

개인 워크플로 또는 설정에 특정한 파일을 커밋하지 않으려면, 
전역 ignore 파일(예: `.gitignore_global`)을 사용하는 것이 좋습니다.

Git 저장소 내에서 pub를 사용하면, pub는 Git에서 무시하는 것과 동일한 파일을 무시합니다. 
예를 들어, `keys.txt`가 포함된 `.gitignore` 파일이 있는 Git 저장소에서, 
`pub publish`를 실행하면 게시된 패키지에 `keys.txt` 파일이 포함되지 않습니다.

`.gitignore` 파일에 대한 자세한 내용은, 
GitHub 도움말 페이지 [파일 무시](https://help.github.com/articles/ignoring-files)를 참조하세요.

### .dart_tool/ {:#dart_tool}

`.dart_tool/` 디렉토리에는 다양한 Dart 도구에서 사용되는 파일이 들어 있습니다.

### pubspec.lock {:#pubspec-lock}

`pubspec.lock` 파일은 Ruby의 `Gemfile.lock`과 비슷한 특수한 케이스입니다.

**일반 패키지의 경우**, `pubspec.lock` 파일을 **커밋하지 마세요**. 
`pubspec.lock` 파일을 재생성하면, 패키지를 종속성의 최신 호환 버전과 테스트할 수 있습니다.

**애플리케이션 패키지의 경우**, `pubspec.lock` 파일을 커밋하는 것이 좋습니다. 
`pubspec.lock` 파일을 버전 관리하면, 전이 종속성에 대한 변경 사항이 명시적으로 적용됩니다. 
`dart pub upgrade` 또는 `pubspec.yaml`의 변경으로 인해 종속성이 변경될 때마다, 
그 차이가 잠금 파일에 나타납니다.
