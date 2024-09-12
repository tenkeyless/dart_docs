---
title: dart pub publish
# description: Use dart pub publish to publish your Dart package to the pub.dev site.
description: dart pub publish를 사용하면 Dart 패키지를 pub.dev 사이트에 게시할 수 있습니다.
---

_Publish_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub publish [options]
```

이 명령은 누구나 다운로드하여 사용할 수 있도록 [pub.dev 사이트]({{site.pub}})에 패키지를 게시합니다. 
패키지를 게시하기 위해 준비하는 방법과, 
포함하거나 제외해야 할 파일에 대한 자세한 내용은 [패키지 게시](/tools/pub/publishing)를 참조하세요.

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--dry-run` 또는 `-n` {:#dry-run-or-n}

이를 통해, pub는 검증 프로세스를 거치지만, 실제로 패키지를 업로드하지는 않습니다. 
이는 실제로 공개하기 전에, 패키지가 모든 게시 요구 사항을 충족하는지 확인하고 싶을 때 유용합니다.

### `--force` 또는 `-f` {:#force-or-f}

이를 통해, pub는 게시하기 전에 확인을 요청하지 않습니다. 
일반적으로 패키지 내용을 보여주고 업로드를 확인하도록 요청합니다.

패키지에 오류가 있는 경우, pub는 업로드하지 않고 오류와 함께 종료됩니다. 
경고가 발생하는 경우, 패키지가 *업로드됩니다*. 
업로드하기 전에 패키지에 경고가 없는지 확인하려면 `--force`를 사용하지 않거나, 
먼저 `--dry-run`을 사용하세요.

### `--skip-validation`

클라이언트 측 검증 프로세스를 거치지 않고, 종속성을 해결하지 않고 게시합니다. 
이는 검증이 실패하는 이유를 알고, 특정 문제를 피하고자 하는 고급 사용자에게 유용합니다.

**예:** pub.dev에 게시할 때 새로 게시된 패키지가 사용 가능해지는 데 몇 분이 걸릴 수 있습니다. 
따라서, 두 개의 종속 패키지를 게시하는 경우, 두 번째 패키지가 첫 번째 패키지에 종속됩니다. 
첫 번째 패키지와 두 번째 패키지를 게시하는 사이에 몇 분을 기다리거나, 
`--skip-validation`을 사용하여 클라이언트 측 검증을 피함으로써, 
두 번째 패키지를 즉시 게시할 수 있습니다.

{% render 'pub-problems.md' %}
