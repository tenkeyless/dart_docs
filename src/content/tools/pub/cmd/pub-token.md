---
title: dart pub token
description: 패키지 저장소에 대한 인증 토큰을 관리합니다.
---

`dart pub token` 하위 명령은 토큰 저장소를 관리합니다. 
패키지를 [게시](pub-lish)하고 종속성을 [검색](pub-get)할 때, 
`dart pub` 명령은 토큰을 사용하여 타사 서버에 대해 인증합니다.

이 토큰은 [사용자 전체 구성 디렉터리][config-dir]에 저장됩니다. 
`dart pub token` 하위 명령에는 [`add`][], [`list`][] 및 [`remove`][]라는, 
세 가지 하위 명령이 있습니다.

`dart pub` 명령은 _credential_, _token_, _secret_ 및 _secret token_ 이라는 
용어를 서로 바꿔 사용할 수 있다고 생각합니다.

[`add`]: #add-a-new-credential
[`list`]: #return-a-list-of-credentials
[`remove`]: #remove-one-or-more-credentials

## 자격 증명(credential)의 사용 사례 {:#use-case-for-credentials}

private 저장소에 호스팅된 [종속성](/tools/pub/dependencies)이 있는 시나리오를 고려하세요. 
`dart pub get` 명령을 사용하면, 자격 증명을 제공하라는 프롬프트가 *반환될 수* 있습니다.

```console
$ dart pub get
Resolving dependencies... 
https://some-package-repo.com/my-org/my-repo package repository requested authentication!
You can provide credentials using:
    dart pub token add https://some-package-repo.com/my-org/my-repo
```

모든 서버는 아니지만 일부 서버에서는, 토큰을 얻는 방법에 대한 지침이 담긴 메시지를 반환합니다.

## 새로운 자격 증명 추가 {:#add-a-new-credential}

새로운 자격 증명을 만들려면 `dart pub token add` 명령을 사용하세요.

### 현재 세션에 대한 자격 증명 추가 {:#add-a-credential-for-the-current-session}

프롬프트에서 명령줄(`stdin`)에 자격 증명을 입력합니다.

```console
$ dart pub token add https://some-package-repo.com/my-org/my-repo
Enter secret token: <Type token on stdin>
 Requests to "https://some-package-repo.com/my-org/my-repo" will now be 
 authenticated using the secret token.
```

:::note
토큰을 셸 기록에 남기지 않기 위해, 
`dart pub token` 명령은 명령줄 옵션이 아닌 `stdin`에서 입력을 받습니다.
:::

### 모든 세션에 대한 자격 증명 추가 {:#add-a-credential-for-all-sessions}

모든 터미널 세션과 스크립트에 동일한 토큰을 사용하려면, 토큰을 환경 변수에 저장합니다.

1. 토큰을 환경 변수에 저장합니다.

    셸 기록에서 토큰을 숨기세요.
    이를 수행하는 한 가지 방법을 알아보려면 [Medium의 이 게시물][zsh-post]를 참조하세요.

1. 추가한 환경 변수를 활성화하려면, 열려 있는 모든 콘솔을 다시 시작합니다.

1. 환경 변수를 토큰으로 사용하려면, `dart pub token add` 명령을 사용합니다.

   ```console
   $ dart pub token add <hosted-url> --env-var <TOKEN_VAR>
   ```

    이 명령은 `$TOKEN_VAR`에 저장된 토큰을 읽은 다음, 
    원하는 패키지를 호스팅하는 `hosted-url`로 인증하는 데 사용합니다. 
    터미널에 다음 응답이 출력되어야 합니다.

   ```console
   $ dart pub token add https://other-package-repo.com/ --env-var TOKEN_VAR
   Requests to "https://other-package-repo.com/" will now be authenticated using the secret token stored in the environment variable "TOKEN_VAR".
   ```

대부분의 CI 환경은 토큰을 환경 변수에 주입할 수 있습니다. 
방법을 알아보려면, [GitHub Actions][] 또는 [GitLab][]에 대한 문서를 예로 참조하세요.

[GitHub Actions]: https://docs.github.com/actions/security-guides/encrypted-secrets#using-encrypted-secrets-in-a-workflow
[GitLab]: https://docs.gitlab.com/ee/ci/secrets/
[zsh-post]: https://medium.com/@prasincs/hiding-secret-keys-from-shell-history-part-1-5875eb5556cc

## 자격 증명 리스트 반환 {:#return-a-list-of-credentials}

모든 활성 자격 증명 리스트를 보려면, `dart pub token list` 명령을 사용하세요.

```console
$ dart pub token list
You have secret tokens for 2 package repositories:
https://some-package-repo.com/my-org/my-repo
https://other-package-repo.com/
```

## 하나 이상의 자격 증명 제거 {:#remove-one-or-more-credentials}

단일 토큰을 제거하려면 `dart pub token remove` 명령을 사용하세요.

```console
$ dart pub token remove https://other-package-repo.com
Removed secret token for package repository: https://other-package-repo.com
```

모든 토큰을 제거하려면 `remove --all` 옵션과 함께 이전 명령을 사용합니다.

```console
$ dart pub token remove --all
pub-tokens.json is deleted.
Removed 1 secret tokens.
```

{% render 'pub-problems.md' %}

[config-dir]: {{site.repo.dart.org}}/cli_util/blob/71ba36e2554f7b7717f3f12b5ddd33751a4e3ddd/lib/cli_util.dart#L88-L118
