
### Homebrew를 사용하여 설치 {:#install-using-homebrew .no_toc}

Dart SDK를 설치하려면, [Homebrew][]를 사용하세요.

1. 필요한 경우 Homebrew를 설치하세요.

1. [공식 tap][tap]을 추가하세요.

   ```console
   $ brew tap dart-lang/dart
   ```

2. Dart SDK를 설치합니다.

   ```console
   $ brew install dart
   ```

### PATH에 Homebrew 포함 여부 확인 {:#verify-path-includes-homebrew .no_toc}

`PATH`에 **Homebrew `bin` 디렉토리**가 포함되어 있는지 확인하세요. 
올바른 경로를 설정하면, `dart run` 및 `dart format`과 같은 Dart SDK 명령을 사용하기가 간소화됩니다.

`PATH` 구성에 대한 도움말을 받으려면, [Homebrew FAQ][]를 참조하세요.

### Homebrew를 사용하여 업그레이드 {:#upgrade-using-homebrew .no_toc}

Dart의 새로운 릴리스가 출시된 후, 업그레이드하려면:

```console
$ brew upgrade dart
```

### Dart 버전 변경 {:#switch-dart-versions .no_toc}

로컬에 설치된 Dart 릴리스 간에 전환하려면:

1. 전환하려는 버전을 설치합니다.

   예를 들어, Dart 3.1을 설치하려면:

   ```console
   $ brew install dart@3.1
   ```

2. 버전을 전환하려면, 현재 버전의 연결을 해제하고 원하는 버전을 연결합니다.

   ```console
   $ brew unlink dart@<old> \
     && brew unlink dart@<new> \
     && brew link dart@<new>
   ```

### 설치된 Dart 버전 리스트 {:#list-installed-dart-versions .no_toc}

설치된 Dart 버전을 확인하려면:

```console
$ brew info dart
```

### Homebrew를 사용하여 제거 {:#uninstall-using-homebrew .no_toc}

Dart SDK를 제거하려면, [Homebrew][]를 사용하세요.

1. Dart SDK를 제거합니다.

   ```console
   $ brew uninstall dart
   ```

2. 홈 디렉토리에서 Dart 구성 파일을 제거합니다.

   ```dart
   rm -rf  ~/.dart*
   ```

[Homebrew]: https://brew.sh
[tap]: {{site.repo.dart.org}}/homebrew-dart
[Homebrew FAQ]: https://docs.brew.sh/FAQ#my-mac-apps-dont-find-homebrew-utilities
