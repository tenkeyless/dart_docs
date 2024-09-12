---
title: dart fix
# description: Command-line tool for applying analysis fixes and migrating API usages.
description: 분석 수정 사항을 적용하고, API 사용을 마이그레이션하기 위한 명령줄 도구입니다.
---

`dart fix` 명령어는 두 가지 타입의 문제를 찾아서 수정합니다.

* [`dart analyst`][]에서 식별한 분석 문제로, 
  자동화된 수정(때로는 _빠른 수정(quick-fixes)_ 또는 _코드 작업(code actions)_ 이라고 함)이 연관되어 있습니다.

* Dart 및 Flutter SDK의 최신 릴리스로 업데이트할 때, 오래된 API 사용.

:::tip
비디오 형식으로 `dart fix`에 대해 알아보려면, 
**Flutter 디코딩**에 대한 이 [심층 분석][deep dive]을 확인하세요.

{% ytEmbed "OBIuSrg_Quo", "Dart analyze 및 Dart fix 사용" %}
:::

[deep dive]: {{site.yt.watch}}/OBIuSrg_Quo

<a id="usage"></a>
## fix 적용 {:#apply-fixes}

제안된 변경 사항을 미리 보려면, `--dry-run` 플래그를 사용하세요.

```console
$ dart fix --dry-run
```

제안된 변경 사항을 적용하려면, `--apply` 플래그를 사용하세요.

```console
$ dart fix --apply
```

<a id="customization"></a>
## 동작 커스터마이즈 {:#customize}

`dart fix` 명령은 진단에서 식별된 "문제"가 있을 때만 수정을 적용합니다. 
컴파일 오류와 같은 일부 진단은 암묵적으로 활성화되는 반면, 
lint와 같은 다른 진단은 [분석 옵션 파일](/tools/analysis)에서 명시적으로 활성화해야 합니다. 
개별 선호도는 다양하기 때문입니다.

추가 lint를 활성화하여 적용할 수 있는 수정 수를 늘릴 수도 있습니다. 
모든 진단에 연관된 수정이 있는 것은 아니라는 점에 유의하세요.

### 예제 {:#example}

다음과 같은 코드가 있다고 상상해보세요.

```dart
class Vector2d {
  final double x, y;
  Vector2d(this.x, this.y);
}

class Vector3d extends Vector2d {
  final double z;
  Vector3d(final double x, final double y, this.z) : super(x, y);
}
```

Dart 2.17은 슈퍼 초기화자라는 새로운 언어 기능을 도입했습니다. 
이를 통해 `Vector3d` 생성자를 더욱 간결한 스타일로 작성할 수 있습니다.

```dart
class Vector3d extends Vector2d {
  final double z;
  Vector3d(super.x, super.y, this.z);
}
```

기존 코드를 업그레이드하여 이 기능을 사용하도록 `dart fix`를 활성화하고, 
나중에 사용을 잊었을 때 분석기에서 경고하도록 하려면, 
다음과 같이 `analysis_options.yaml` 파일을 구성하세요.

```yaml
linter:
  rules:
    - use_super_parameters
```

또한 코드가 필수 [언어 버전][language version]을 활성화하도록 해야 합니다. 
슈퍼 이니셜라이저는 Dart 2.17에서 도입되었으므로, 
`pubspec.yaml`을 업데이트하여 최소한 하위 SDK 제약 조건에 해당 내용을 포함하도록 합니다.

```yaml
environment:
  sdk: ">=2.17.0 <4.0.0"
```

그러면 제안된 변경 사항을 볼 때 다음 내용이 표시됩니다.

```console
$ dart fix --dry-run
Computing fixes in myapp (dry run)... 9.0s

1 proposed fixes in 1 files.

lib/myapp.dart
  use_super_parameters • 1 fix
```

분석 결과 및 동작 커스터마이즈에 대한 자세한 내용은, 
[정적 분석 커스터마이즈](/tools/analysis)을 참조하세요.

[`dart analyze`]: /tools/dart-analyze
[language version]: /guides/language/evolution#language-versioning

## VS Code 지원 {:#vs-code-support}

VS Code에서 프로젝트를 열면, 
Dart 플러그인이 프로젝트를 스캔하여 `dart fix`가 복구할 수 있는 문제를 찾습니다. 
복구할 문제가 발견되면 VS Code가 알림을 표시합니다.

<img src="/assets/img/tools/vscode/dart_fix_notification.png" width="550" height="175" alt="VS Code notification about 'dart fix'">

`dart pub get` 또는 `dart pub upgrade`를 실행한 후, 
패키지 변경 사항이 `dart fix`가 복구할 수 있는 문제를 추가하는 경우, 
VS Code가 이 메시지를 표시할 수도 있습니다.

`dart fix`를 실행하기 전에 모든 파일을 저장하세요. 
이렇게 하면 Dart가 최신 버전의 파일을 사용합니다.
