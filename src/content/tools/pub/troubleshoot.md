---
# title: Troubleshooting pub
title: pub 문제 해결
# description: Common gotchas you might run into when using pub.
description: pub를 사용할 때 겪을 수 있는 일반적인 문제점은 다음과 같습니다.
---

## 패키지를 게시할 때 "403" 오류가 발생합니다. {:#pub-publish-403}

`pub publish`를 실행하는 동안 다음 오류가 발생합니다.

```plaintext
HTTP error 403: Forbidden
...
You aren't an uploader for package '<foo>'
```

이 문제는 당신의 계정 중 하나에 패키지를 게시할 수 있는 권한이 부여되었지만, 
pub 클라이언트가 다른 계정으로 당신을 등록한 경우 발생할 수 있습니다.

pub 자격 증명 파일을 삭제하여 pub의 인증 프로세스를 재설정할 수 있습니다.

#### Linux {:#pub-credentials-linux}

`$XDG_CONFIG_HOME`이 정의된 경우:

```console
$ rm $XDG_CONFIG_HOME/dart/pub-credentials.json
```

그렇지 않으면:

```console
$ rm $HOME/.config/dart/pub-credentials.json
```

#### macOS {:#pub-credentials-mac}

```console
$ rm $HOME/Library/Application Support/dart/pub-credentials.json
```

#### Windows {:#pub-credentials-windows}

명령 프롬프트를 사용하는 경우:

```cmd
$ del "%APPDATA%\dart\pub-credentials.json"
```

PowerShell을 사용하는 경우:

```ps
$ Remove-Item -Path "%APPDATA%\dart\pub-credentials.json"
```

:::version-note
Dart 2.14 또는 이전 버전에서는, 
[`PUB_CACHE`][] 폴더에 있는 `credentials.json` 파일을 삭제해야 합니다.
:::

[`PUB_CACHE`]: /tools/pub/environment-variables

## 패키지를 게시할 때 "UnauthorizedAccess" 오류가 발생합니다. {:#pub-publish-unauthorized}

`pub publish`를 실행하는 동안 다음 오류가 발생합니다.

```plaintext
UnauthorizedAccess: Unauthorized user: <username> is not allowed to upload versions to package '<foo>'.
```

패키지의 새 버전을 게시할 권한이 있는 사람 리스트에 없으면 이 메시지가 표시됩니다. 
[업로더](/tools/pub/publishing#uploaders)를 참조하세요.

## Pub 빌드가 HttpException 오류로 실패합니다. {:#pub-get-fails}

`pub build`를 실행하는 동안 다음과 유사한 HttpException 오류가 발생합니다.

```plaintext
Pub build failed, [1] IsolateSpawnException: 'HttpException: Connection closed while receiving data,
...
library handler failed
...
```

이는 AVG 2013 인터넷 보안 제품군과 같은 일부 바이러스 백신 소프트웨어로 인해 발생할 수 있습니다. 
보안 제품군 설명서를 확인하여, 이 기능을 일시적으로 비활성화하는 방법을 확인하세요. 
예를 들어, [AVG 구성 요소 비활성화 방법](https://support.avg.com/SupportArticleView?urlName=How-to-disable-AVG)을 참조하세요.

## 회사 방화벽 뒤에서 Pub get이 실패합니다. {:#pub-get-fails-from-behind-a-corporate-firewall}

명령줄에서, pub는 `http_proxy` 및 `https_proxy` 환경 변수를 존중합니다. 
다음과 같이 프록시 서버 환경 변수를 설정할 수 있습니다.

Linux/macOS에서:

```console
$ export https_proxy=hostname:port
```

Windows 명령 프롬프트에서:

```cmd
$ set https_proxy=hostname:port
```

Windows PowerShell에서:

```ps
$ $Env:https_proxy="hostname:port"
```

프록시에 자격 증명이 필요한 경우, 다음과 같이 설정할 수 있습니다.

Linux/macOS에서:

```console
$ export https_proxy=username:password@hostname:port
```

Windows 명령 프롬프트에서:

```cmd
$ set https_proxy=username:password@hostname:port
```

Windows PowerShell에서:

```ps
$ $Env:https_proxy="username:password@hostname:port"
```

## 로그인 후 localhost에 접근할 수 없음 {:#localhost-unreachable-after-sign-in}

컨테이너나 SSH 세션에서 `dart pub publish`를 실행할 때, 
`dart pub`가 수신하는 `localhost`는 브라우저에서 액세스할 수 있는 `localhost`와 다를 수 있습니다. 
브라우저를 사용하여 로그인할 수 있지만, 
브라우저는 `http://localhost:<port>?code=...`에 도달할 수 없다고 불평합니다.

명령줄을 사용하여 로그인을 완료하는 이 해결 방법을 시도해 보세요.

1. 터미널 창에서, `dart pub publish`를 실행합니다.
2. 나타나는 브라우저 창에서 로그인합니다. <br> 
   브라우저가 _새로운 localhost URL_(`http://localhost:<port>?code=...`)로 리디렉션되지만, 
   URL에 도달할 수 없다고 불평합니다.
3. 브라우저에서 _새로운 localhost URL_ 을 복사합니다.
4. `dart pub publish`가 호출된 것과 동일한 컨테이너 또는 동일한 호스트의 다른 터미널 창에서 `curl` 명령을 
   사용하여, _새로운 localhost URL_ 을 사용하여 로그인을 완료합니다.

   ```console
   $ curl 'http://localhost:<port>?code=...'
   ```

## 패키지를 찾으려고 시도하는 중 소켓 오류가 발생합니다. {:#pub-get-socket-error}

인터넷에 접속할 수 없거나, ISP가 `pub.dev`를 차단하거나, 
보안 소프트웨어가 `dart`에서 인터넷 접속을 차단하는 경우 다음 오류가 발생할 수 있습니다.

```plaintext
Got socket error trying to find package ... at https://pub.dev.
pub get failed (server unavailable) -- attempting retry 1 in 1 second...
```

인터넷 연결을 확인하고, `dart`의 인터넷 접근을 차단하는 방화벽이나 기타 보안 소프트웨어가 없는지 확인하세요.

<details>
 <summary>
   <b>Kaspersky Internet Security에 대한 자세한 지침</b>
  </summary>

  메뉴 바에서 _Kaspersky Internet Security_ 보호를 해제한 경우, 
  VPN 애플리케이션 필터 `sysextctrld`가 백그라운드에서 계속 실행됩니다. 
  이 필터로 인해 `pub.dev`에 연결하지 못합니다. 
  이 문제를 해결하려면 `https://pub.dev`와 `https://pub.dartlang.org`를 모두 신뢰할 수 있는 영역에 추가합니다.

   1. Kaspersky Internet Security를 ​​엽니다.
   2. **Privacy** 아이콘을 클릭합니다.
   3. **Block website tracking** 섹션에서 **Preferences** 버튼을 클릭합니다.
   4. 상단 아이콘 바에서 **Threats**을 선택합니다.
   5. **Threats**에서 **Trusted Zone**을 클릭합니다.
   6. **Trusted web addresses** 탭을 선택합니다.
   7. **+** 버튼을 클릭하고 URL `https://pub.dev`를 추가합니다.
   8. **OK**을 클릭합니다.
   9. `https://pub.dartlang.org`에 대해 이전 두 단계를 반복합니다.

</details>
