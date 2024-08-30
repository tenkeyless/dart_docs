
### Linux 패키지 관리자를 사용하여 설치 {:#install-using-a-linux-package-manager .no_toc}

Ubuntu 또는 Debian에 Dart SDK를 설치하는 데는 두 가지 옵션이 있습니다.

* [apt-get](#install-using-the-apt-get-package-manager) 명령을 사용합니다.
* [`.deb`](#install-as-a-debian-package) 패키지를 다운로드하고 `dpkg` 명령을 실행합니다.

### `apt-get` 패키지 관리자를 사용하여 설치 {:#install-using-the-apt-get-package-manager .no_toc}

`apt-get`으로 Dart를 설치하려면 다음 단계를 수행합니다. 첫 번째 설치에는 1~3단계만 필요합니다.

1. 패키지 인덱스 파일을 업데이트하고, 보안 HTTP 패키지를 설치합니다.

   ```console
   $ sudo apt-get update && sudo apt-get install apt-transport-https
   ```

2. Google Linux GPG 공개 키를 다운로드하여 추가합니다.

   ```console
   $ wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub \
     | sudo gpg  --dearmor -o /usr/share/keyrings/dart.gpg
   ```

3. Linux 시스템에 Dart 패키지 저장소를 추가합니다.

   ```console
   $ echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' \
     | sudo tee /etc/apt/sources.list.d/dart_stable.list
   ```

4. 다음의 `sudo apt-get` 명령을 사용하세요.

   ```console
   $ sudo apt-get update && sudo apt-get install dart
   ```

### Debian 패키지로 설치 {:#install-as-a-debian-package .no_toc}

Dart SDK를 Debian 패키지(`*.deb`)로 설치하려면 다음 단계를 수행합니다.

1. Dart SDK [Debian 패키지](#){:.debian-link-stable}를 다운로드합니다.

1. `sudo dpkg` 명령을 사용하여 `*.deb` 패키지를 설치합니다.

   ```console
   $ sudo dpkg -i dart_3.4.0-1_amd64.deb
   ```

   `dart_3.4.0-1_amd64.deb`를 현재 파일 이름으로 바꾸세요.

## Dart SDK 업그레이드 {:#upgrade-the-dart-sdk .no_toc}

SDK를 설치하는 데 사용한 것과 동일한 명령을 사용하세요.

### `apt-get`를 사용하여 업그레이드 {:#upgrade-using-apt-get .no_toc}

`apt-get`으로 Dart SDK를 설치한 경우, 다음 `sudo apt-get` 명령을 사용하세요.

```console
$ sudo apt-get update && sudo apt-get install dart
```

### `dpkg`를 사용하여 업그레이드 {:#upgrade-using-dpkg .no_toc}

`dpkg`로 Dart SDK를 설치한 경우, `sudo dpkg` 명령을 사용하세요.

```console
$ sudo dpkg -i dart_3.2.6-1_amd64.deb
```

`dart_3.4.0-1_amd64.deb`를 새로운 업그레이드의 파일 이름으로 바꾸세요.

## Dart SDK 제거 {:#uninstall-the-dart-sdk .no_toc}

### `apt-get`를 사용하여 제거 {:#uninstall-using-apt-get .no_toc}

`apt-get`으로 Dart SDK를 설치한 경우, `sudo apt-get remove` 명령을 사용합니다.

1. `sudo apt-get remove` 명령을 사용합니다.

   ```console
   $ sudo apt-get remove -y dart
   ```

2. 홈 디렉토리에서 Dart 구성 파일을 제거합니다.

   ```console
   $ rm -rf  ~/.dart*
   ```

### `dpkg`를 사용하여 제거 {:#uninstall-using-dpkg .no_toc}

`dpkg`로 Dart SDK를 설치한 경우, `sudo dpkg --purge` 명령을 사용합니다.

1. `sudo dpkg --purge` 명령을 사용합니다.

   ```console
   $ sudo dpkg --purge dart
   ```

   이렇게 하면 구성 파일도 동시에 제거됩니다.

2. SDK가 제거되었는지 확인하세요.

   ```console
   $ dpkg -l | grep dart
   ```

[sudo]: https://www.sudo.ws/
