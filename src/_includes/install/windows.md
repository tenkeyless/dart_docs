
### Chocolatey를 사용하여 설치 {:#install-using-chocolatey .no_toc}

Dart SDK를 설치하려면, [Chocolatey][Chocolatey]를 사용하세요. 
Chocolatey에는 [승격된 권한][elevated permissions]이 필요합니다.

1. Chocolatey를 설치하세요.

1. [PowerShell][]을 승격된 권한으로 시작하세요.

   ```ps
   PS C:\> choco install dart-sdk
   ```

### 기본 설치 경로 변경 {:#change-default-install-path .no_toc}

기본적으로, Chocolatey는 SDK를 `C:\tools\dart-sdk`에 설치합니다. 
해당 위치를 변경하려면 [`ChocolateyToolsLocation`][] 환경 변수를 원하는 설치 디렉토리로 설정합니다.

### PATH에 Dart 포함 여부 확인 {:#verify-your-path-includes-dart .no_toc}

Dart를 실행할 수 있는지 확인하세요.

```ps
PS C:\> dart --version
Dart SDK version: 3.2.4 (stable) (Thu Dec 21 19:13:53 2023 +0000) on "win_x64"
```

개발 머신이 Dart 버전을 반환하지 않으면, PATH에 SDK 위치를 추가합니다.

1. Windows 검색 상자에 `env`를 입력합니다.
2. **Edit the system environment variables**을 클릭합니다.
3. **Environment Variables...**를 클릭합니다.
4. 사용자 변수 섹션에서, **Path**를 선택하고 **Edit...**을 클릭합니다.
5. **New**를 클릭하고, `dart-sdk` 디렉터리 경로를 입력합니다.
6. 방금 연 각 창에서 **Apply** 또는 **OK**을 클릭하여 닫고 경로 변경을 적용합니다.

### Chocolatey를 사용하여 업그레이드 {:#upgrade-using-chocolatey .no_toc}

Dart SDK를 업그레이드하려면, 다음 명령을 사용하세요.

```ps
PS C:\> choco upgrade dart-sdk
```

### Chocolatey를 사용하여 제거 {:#uninstall-using-chocolatey .no_toc}

Dart SDK를 제거하려면, 다음 단계를 수행합니다.

1. 상승된 권한으로 [PowerShell][]을 시작합니다.

1. 다음 명령을 사용합니다.

   ```ps
   PS C:\> choco uninstall dart-sdk
   ```

1. 홈 디렉토리에서 Dart 구성 파일을 제거합니다.

   ```ps
   PS C:\> Remove-Item -Recurse -Force ^
        -Path $env:LOCALAPPDATA\.dartServer,$env:APPDATA\.dart,$env:APPDATA\.dart-tool
   ```

[elevated permissions]: https://www.thewindowsclub.com/elevated-privileges-windows
[PowerShell]: https://www.thewindowsclub.com/how-to-open-an-elevated-powershell-prompt-in-windows-10
[Chocolatey]: https://chocolatey.org
[`ChocolateyToolsLocation`]: https://stackoverflow.com/questions/19752533/how-do-i-set-chocolatey-to-install-applications-onto-another-drive/68314437#68314437
