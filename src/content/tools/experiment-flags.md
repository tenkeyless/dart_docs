---
# title: Experiment flags
title: 실험 플래그
# description: Using experiment flags with Dart tools.
description: Dart 도구를 사용하여 실험 플래그를 사용합니다.
---

Dart SDK에는 종종 실험적 기능이 포함되어 있으며, 
Dart 도구에 플래그를 전달하여 이러한 기능을 사용해 볼 수 있습니다.

:::warning
프로덕션 코드에 실험을 사용하지 마세요. 
실험에는 중단 변경이 있거나 통지 없이 제거될 수 있습니다.
:::


## 명령줄 도구로 실험 플래그 사용 {:#using-experiment-flags-with-command-line-tools}

Dart SDK [명령줄 도구](/tools/sdk)로 실험을 사용하려면 해당 플래그를 도구에 전달합니다. 
예를 들어, `super-mixins` 및 `no-slow-checks` 실험을 활성화하려면, 
해당 플래그를 `dart` 명령에 추가합니다.

```console
$ dart run --enable-experiment=super-mixins,no-slow-checks bin/main.dart
```


## Dart 분석기로 실험 플래그 사용 (command-line and IDE) {:#using-experiment-flags-with-the-dart-analyzer-command-line-and-ide}

분석에 영향을 미치는 실험을 활성화하려면, 
[분석 옵션 파일][analysis options file]에서 `enable-experiment` 키를 사용합니다. 
다음은 `analysis_options.yaml`에서 `super-mixins` 및 `no-slow-checks` 실험을 활성화하는 예입니다.

```yaml title="analysis_options.yaml"
analyzer:
  enable-experiment:
    - super-mixins
    - no-slow-checks
```

[analysis options file]: /tools/analysis#the-analysis-options-file


## IDE로 실험 플래그 사용 {:#using-experiment-flags-with-ides}

IDE에서 앱 실행이나 디버깅과 관련된 실험을 활성화하려면, 실행 구성을 편집하세요.

### Visual Studio Code {:#visual-studio-code}

`launch.json`의 `configurations`에서, 
원하는 플래그를 포함하는 새로운 `toolArgs` 키를 추가합니다. 예:

```json title="launch.json"
 "configurations": [
        {
            "name": "Dart",
            "program": "bin/main.dart",
            "request": "launch",
            "type": "dart",
            "toolArgs": [
                "--enable-experiment=super-mixins,no-slow-checks",
            ],
        }
    ]
```

자세한 내용은 [VS Code 실행 구성][VSC instructions]에 대한 문서를 참조하세요.

[VSC instructions]: https://code.visualstudio.com/docs/editor/debugging#_launch-configurations


### Android Studio {:#android-studio}

`VMOptions` 아래에 원하는 플래그를 추가합니다. 예:

```xml
<component name="ProjectRunConfigurationManager">
  <configuration default="false" name="Run main" type="DartCommandLineRunConfigurationType" factoryName="Dart Command Line Application">
    <option name="VMOptions" value="--enable-experiment=non-nullable" />
    <option name="filePath" value="$PROJECT_DIR$/bin/main.dart" />
    <method v="2" />
  </configuration>
</component>
```

자세한 내용은, [Android Studio 실행/디버그 구성][AS instructions]에 대한 지침을 참조하세요.

[AS instructions]: {{site.android-dev}}/studio/run/rundebugconfig


## 더 많은 정보 {:#more-information}

* 실험의 전체 리스트는, Dart SDK 파일 [`experimental_features.yaml`][`experimental_features.yaml`.]을 참조하세요.
* 실험 플래그에 대한 절차 및 기대 사항에 대한 정보는 
  [실험 플래그 뒤에 있는 변경 사항에 대한 프로세스][flags] 문서를 참조하세요.

[`experimental_features.yaml`.]: {{site.repo.dart.sdk}}/blob/main/tools/experimental_features.yaml
[flags]: {{site.repo.dart.sdk}}/blob/main/docs/process/experimental-flags.md

