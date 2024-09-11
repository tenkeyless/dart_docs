---
# title: Glossary of package terms
title: 패키지 용어집
# description: A glossary of terms relating to Dart's package management tool, pub.
description: A glossary of terms relating to Dart's package management tool, pub.
---

다음 용어는 [패키지 관리][package management] 및 [pub 도구][pub tool] 문서에서 사용됩니다.

[package management]: /guides/packages
[pub tool]: /tools/pub/cmd

## 애플리케이션 패키지 {:#application-package}

프로그램이나 앱을 포함하는 패키지로, [메인 진입점][main entrypoint]이 있습니다. 
명령줄이나 브라우저에서 직접 실행되도록 의도되었습니다.

애플리케이션 패키지는 다른 패키지에 [종속성][dependencies]이 있을 수 있지만, 
결코 스스로에 종속되지 않습니다. 
일반적인 [패키지][packages]와 달리, 공유되도록 의도되지 않았습니다.

애플리케이션 패키지는 [lockfiles][]를 소스 제어에 체크인해야 하므로, 
애플리케이션에서 작업하는 모든 사람과 애플리케이션이 배포된 모든 위치에 일관된 종속성 집합이 있습니다. 
종속성이 lockfile에 의해 제한되기 때문에, 
애플리케이션 패키지는 일반적으로 종속성 [버전 제약][version constraints]에 대해 `any`를 지정합니다.

[main entrypoint]: #entrypoint
[dependencies]: #dependency
[packages]: #package
[lockfiles]: #lockfile
[version constraints]: #version-constraint

## 컨텐츠 해시 {:#content-hashes}

pub.dev 저장소는 호스팅하는 각 패키지 버전의 sha256 해시를 유지 관리합니다. 
Pub 클라이언트는 이 해시를 사용하여 다운로드한 패키지의 무결성을 검증하고, 
저장소의 변경으로부터 보호할 수 있습니다.

`dart pub get`이 패키지를 다운로드하면, 
다운로드한 아카이브의 해시를 계산합니다. 
호스팅된 각 종속성의 해시는 [lockfile][]의 [resolution][]과 함께 저장됩니다.

pub 클라이언트는 이 컨텐츠 해시를 사용하여, 
다른 컴퓨터에서 동일한 lockfile을 사용하여, 
`dart pub get`을 다시 실행하면 정확히 동일한 패키지가 사용되는지 확인합니다.

잠긴 해시가 현재 pub 캐시에 있는 내용과 일치하지 않으면, 
pub은 아카이브를 다시 다운로드합니다. 
여전히 일치하지 않으면, lockfile이 업데이트되고 경고가 출력됩니다. 예를 들어:

```plaintext
$ dart pub get
Resolving dependencies...
[!Cached version of foo-1.0.0 has wrong hash - redownloading.!]
 ~ foo 1.0.0 (was 1.0.0)
[!The existing content-hash from pubspec.lock doesn't match contents for:!]
 * foo-1.0.0 from "pub.dev"
This indicates one of:
 * The content has changed on the server since you created the pubspec.lock.
 * The pubspec.lock has been corrupted.
 
[!The content-hashes in pubspec.lock has been updated.!]

For more information see:
https://dart.dev/go/content-hashes

Changed 1 dependency!
```

업데이트된 컨텐츠 해시는 버전 제어 diff에 표시되며, 의심스러워질 것입니다.

불일치를 경고 대신 오류로 만들려면, 
[`dart pub get --enforce-lockfile`][]을 사용합니다. 
lockfile을 업데이트하지 않고, 
동일한 해시를 가진 패키지 아카이브를 찾을 수 없는 경우, resolution이 실패하게 됩니다.

```plaintext
$ dart pub get [!--enforce-lockfile!]
Resolving dependencies...
Cached version of foo-1.0.0 has wrong hash - redownloading.
~ foo 1.0.0 (was 1.0.0)
The existing content-hash from pubspec.lock doesn't match contents for:
 * foo-1.0.0 from "pub.dev"

This indicates one of:
 * The content has changed on the server since you created the pubspec.lock.
 * The pubspec.lock has been corrupted.

For more information see:
https://dart.dev/go/content-hashes
[!Would change 1 dependency.!]
[!Unable to satisfy `pubspec.yaml` using `pubspec.lock`.!]

To update `pubspec.lock` run `dart pub get` without
`--enforce-lockfile`.
```

[resolution]: /tools/pub/cmd/pub-get
[lockfile]: #lockfile
[`dart pub get --enforce-lockfile`]: /tools/pub/cmd/pub-get#enforce-lockfile

## 의존성 {:#dependency}

패키지가 의존하는 다른 패키지. 
패키지가 다른 패키지에서 코드를 가져오려면, 해당 패키지가 종속성이어야 합니다. 
종속성은 패키지의 [pubspec][]에 지정되고, 
[패키지 의존성][Package dependencies]에 설명되어 있습니다.

패키지에서 사용하는 종속성을 보려면 [`pub deps`][]를 사용합니다.

[pubspec]: /tools/pub/pubspec
[Package dependencies]: /tools/pub/dependencies
[`pub deps`]: /tools/pub/cmd/pub-deps

## 진입점 {:#entrypoint}

Dart의 일반적인 맥락에서, _entrypoint_ 는 Dart 구현에서 직접 호출되는 Dart 라이브러리입니다. 
`<script>` 태그에서 Dart 라이브러리를 참조하거나, 
독립 실행형 Dart VM에 명령줄 인수로 전달하면 해당 라이브러리가 진입점입니다. 
즉, 일반적으로 `main()`을 포함하는 `.dart` 파일입니다.

pub의 맥락에서, _entrypoint 패키지_ 또는 _root 패키지_ 는 종속성 그래프의 루트입니다. 
일반적으로 애플리케이션이 됩니다. 앱을 실행하면, 진입점 패키지가 됩니다. 
종속된 다른 모든 패키지는 해당 맥락에서 진입점이 되지 않습니다.

패키지는 일부 맥락에서는 진입점이 될 수 있지만, 다른 맥락에서는 그렇지 않을 수 있습니다. 
앱에서 패키지 `A`를 사용한다고 가정해 보겠습니다. 
앱을 실행할 때 `A`는 진입점 패키지가 아닙니다. 
그러나, `A`로 이동하여 테스트를 실행하면, 해당 맥락에서 앱이 관련되지 않으므로 진입점이 *됩니다*.

## 진입점 디렉토리 {:#entrypoint-directory}

패키지 내부에 [Dart 진입점](#entrypoint)을 포함할 수 있는 디렉토리.

Pub에는 `benchmark`, `bin`, `example`, `test`, `tool`, `web`(그리고 [Flutter 앱][Flutter apps]의 경우 `lib`) 디렉토리 리스트가 있습니다. 
이러한 디렉토리의 모든 하위 디렉토리(`bin` 제외)에도 진입점이 포함될 수 있습니다.

[Flutter apps]: {{site.flutter-docs}}/packages-and-plugins/developing-packages

## 즉각적인 종속성 {:#immediate-dependency}

패키지가 직접 사용하는 [종속성](#dependency). 
pubspec에 나열한 종속성은 패키지의 직접 종속성입니다. 
다른 모든 종속성은 [전이적(transitive) 종속성](#transitive-dependency)입니다.

## 라이브러리 {:#library}

라이브러리는 단일 기본 파일과 임의의 수의 [파트][parts]로 구성된 단일 컴파일 유닛입니다. 
라이브러리는 자체 private 범위를 갖습니다.

[parts]: /resources/glossary#part-file

## Lockfile {:#lockfile}

`pubspec.lock`이라는 이름의 파일로, 
패키지가 의존하는 모든 즉각적 및 전이적 종속성에 대한 구체적인 버전과 기타 식별 정보를 지정합니다.

즉각적 종속성만 나열하고 버전 범위를 허용하는 pubspec과 달리, 
lockfile은 전체 종속성 그래프를 특정 버전의 패키지에 종합적으로 고정합니다. 
lockfile은 애플리케이션에서 사용하는 패키지의 정확한 구성을 다시 만들 수 있도록 합니다.

lockfile은 [`pub get`](/tools/pub/cmd/pub-get), [`pub upgrade`](/tools/pub/cmd/pub-upgrade) 또는 [`pub downgrade`](/tools/pub/cmd/pub-downgrade)를 실행할 때, 
pub에서 자동으로 생성됩니다. 
Pub에는 future resolutions 중에 확인할 각 패키지에 대한 [컨텐츠 해시][content hash]가 포함됩니다.

패키지가 [애플리케이션 패키지][application package]인 경우, 일반적으로 소스 제어에 체크인합니다. 
일반 패키지의 경우, 일반적으로 체크인하지 않습니다.

[content hash]: #content-hashes

<a id="library-package"></a>

## 패키지 {:#package}

디렉토리 아래에 있는 [라이브러리][libraries] 모음으로, 해당 디렉토리 루트에 [pubspec.yaml]이 있습니다.

패키지는 다른 패키지에 대한 [종속성](#dependency)을 가질 수 있으며, *그리고* 그 자체가 종속성일 수 있습니다. 
패키지의 `/lib` 디렉토리에는 다른 패키지가 import해서 사용할 수 있는, 
[공개 라이브러리][public libraries]가 들어 있습니다. 
직접 실행할 스크립트도 포함될 수 있습니다. 
다른 패키지에서 의존하도록 의도되지 않은 패키지는 [애플리케이션 패키지][application package]입니다. 
공유 패키지는 pub.dev에 [게시][published]되지만, 게시되지 않은 패키지도 있을 수 있습니다.

라이브러리는 다양한 종속성 버전을 지원해야 하므로, 패키지의 [lockfile][]을 소스 제어에 체크인하지 마세요. 
패키지의 [즉각적인 종속성][immediate dependencies]의 [버전 제약 조건][version constraints]은 가능한 한 넓어야 하지만, 
종속성이 테스트된 버전과 호환되도록 해야 합니다.

[시맨틱 버전 관리](https://semver.org/spec/v2.0.0-rc.1.html)는 라이브러리가 이전 버전과 호환되지 않는 변경 사항에 대해 주요 버전 번호를 증가시켜야 하므로, 
패키지는 일반적으로 종속성 버전이 테스트된 버전보다 크거나 같고, 다음 주요 버전보다 작기를 요구합니다. 
따라서, 라이브러리가 (가상의) `transmogrify` 패키지에 종속되어 있고 버전 1.2.1에서 테스트한 경우, 
버전 제약 조건은 [`^1.2.1`][]이 됩니다.

[libraries]: #library
[pubspec.yaml]: /tools/pub/pubspec
[public libraries]: /tools/pub/package-layout#public-libraries
[application package]: #application-package
[published]: /tools/pub/publishing
[lockfile]: #lockfile
[version constraints]: #version-constraint
[immediate dependencies]: #immediate-dependency
[`^1.2.1`]: /tools/pub/dependencies#caret-syntax

## SDK 제약 {:#sdk-constraint}

패키지가 지원한다고 선언한 Dart SDK 자체의 선언된 버전. 

SDK 제약 조건은 일반적인 [버전 제약 조건](#version-constraint) 구문을 사용하여 지정되지만, 
[pubspec](/tools/pub/pubspec#sdk-constraints)의 특수 _environment_ 섹션에서 지정됩니다.

## 소스 {:#source}

pub에서 패키지를 가져올 수 있는 장소의 종류입니다. 
소스는 pub.dev 사이트나 특정 Git URL과 같은 특정 장소가 아닙니다. 
각 소스는 어떤 방식으로든 패키지에 액세스하는 일반적인 절차를 설명합니다. 
예를 들어, _git_ 은 하나의 소스입니다. 
git 소스는 Git URL이 주어지면 패키지를 다운로드하는 방법을 알고 있습니다. 
여러 가지 다른 [지원되는 소스](/tools/pub/dependencies#dependency-sources)를 사용할 수 있습니다.

## 시스템 캐시 {:#system-cache}

pub가 원격 패키지를 받으면, pub가 관리하는 단일 _시스템 캐시_ 디렉토리에 다운로드합니다. 
Mac과 Linux에서 이 디렉토리는 기본적으로 `~/.pub-cache`입니다. 
Windows에서 디렉토리는 기본적으로 `%LOCALAPPDATA%\Pub\Cache`이지만, 
정확한 위치는 Windows 버전에 따라 다를 수 있습니다. 
[PUB_CACHE](/tools/pub/environment-variables) 환경 변수를 사용하여, 다른 위치를 지정할 수 있습니다.

패키지가 시스템 캐시에 있으면, 
pub는 애플리케이션에서 사용하는 각 패키지를 캐시의 해당 패키지에 매핑하는, 
`package_config.json` 파일을 만듭니다.

주어진 버전의 패키지를 한 번만 다운로드하면 원하는 만큼 많은 패키지에서 재사용할 수 있습니다. 
캐시된 패키지를 사용하도록 `--offline` 플래그를 지정하면, 
네트워크에 액세스하지 않고도 `package_config.json` 파일을 삭제하고 다시 생성할 수 있습니다.


## 전이적 종속성 {:#transitive-dependency}

패키지의 종속성 중 하나가 필요로 하기 때문에 패키지가 간접적으로 사용하는 종속성입니다. 
패키지가 A에 종속되고, A는 B에 종속되고, B는 C에 종속되는 경우, 
A는 [즉각적 종속성](#immediate-dependency)이고 B와 C는 전이적 종속성입니다.


## 업로더 {:#uploader}

패키지에 대한 관리 권한(administrative permissions)이 있는 사람. 

패키지 업로더는 패키지의 새 버전을 업로드할 수 있으며, 
해당 패키지에 대해 [다른 업로더를 추가 및 제거](/tools/pub/publishing#uploaders)할 수도 있습니다.

패키지에 검증된 게시자(verified publisher)가 있는 경우, 게시자의 모든 구성원이 패키지를 업로드할 수 있습니다.


## 검증된 게시자 {:#verified-publisher}

패키지 세트를 소유한 한 명 이상의 사용자. 

각 검증된 게시자는 **dart.dev**와 같은, 검증된 도메인 이름으로 식별됩니다. 
검증된 게시자에 대한 일반 정보는 [검증된 게시자 페이지][verified publishers page]를 참조하세요. 
검증된 게시자를 만들고 패키지를 전송하는 방법에 대한 자세한 내용은 [패키지 게시][publishing packages]에 대한 설명서를 참조하세요.

[verified publishers page]: /tools/pub/verified-publishers
[publishing packages]: /tools/pub/publishing#verified-publisher

## 버전 제약 {:#version-constraint}

패키지의 각 [종속성](#dependency)에 적용되는 제약 조건으로, 
패키지가 해당 종속성의 어떤 버전을 사용해야 하는지 지정합니다. 
이는 단일 버전(`0.3.0`) 또는 버전 범위(`^1.2.1`)일 수 있습니다. 
`any`도 허용되지만, 성능상의 이유로 권장하지 않습니다.

자세한 내용은 [버전 제약 조건](/tools/pub/dependencies#version-constraints)을 참조하세요.

[패키지](#package)는 항상 모든 종속성에 대한 버전 제약 조건을 지정해야 합니다. 
반면 [애플리케이션 패키지](#application-package)는 [lockfile](#lockfile)을 사용하여, 
종속성 버전을 관리하므로 일반적으로 모든 버전의 종속성을 허용해야 합니다.

자세한 내용은 [Pub 버전 관리 철학](/tools/pub/versioning)을 참조하세요.

