---
title: Mixins
description: Dart에서 클래스에 기능을 추가하는 방법을 알아보세요.
toc: false
prevpage:
  url: /language/extend
  # title: Extend a class
  title: 클래스 확장
nextpage:
  url: /language/enums
  title: Enums
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

믹스인(Mixins)은 여러 클래스 계층에서 재사용할 수 있는 코드를 정의하는 방법입니다. 
믹스인은 대량으로 멤버 구현을 제공하기 위한 것입니다.

믹스인을 사용하려면, `with` 키워드 뒤에 하나 이상의 믹스인 이름을 사용합니다. 
다음 예에서는, 믹스인을 사용하는(또는 믹스인의 하위 클래스인) 두 클래스를 보여줍니다.

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (musician-and-maestro)" replace="/(with.*) \{/[!$1!] {/g"?>
```dart
class Musician extends Performer [!with Musical!] {
  // ···
}

class Maestro extends Person [!with Musical, Aggressive, Demented!] {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```

믹스인을 정의하려면, `mixin` 선언을 사용합니다. 
믹스인 _과_ 클래스를 모두 정의해야 하는 드문 경우에는, 
[`mixin class` 선언](#class-mixin-or-mixin-class)을 사용할 수 있습니다.

믹스인과 믹스인 클래스는 `extends` 절을 가질 수 없으며, 생성 생성자를 선언해서는 안 됩니다.

예를 들어:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (musical)"?>
```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

## 믹스인(mixin)이 자체적으로 호출할 수 있는 멤버를 지정하세요 {:#specify-members-a-mixin-can-call-on-itself}

때때로 믹스인은 메서드를 호출하거나 필드에 액세스할 수 있어야 하지만, 
그 멤버를 직접 정의할 수는 없습니다.
(믹스인은 생성자 매개변수를 사용하여 자체 필드를 인스턴스화할 수 없기 때문입니다)

다음 섹션에서는, 
믹스인의 모든 하위 클래스가 믹스인의 동작이 의존하는 모든 멤버를 정의하도록 하는, 
다양한 전략을 다룹니다.

### 믹스인에서 추상 멤버 정의 {:#define-abstract-members-in-the-mixin}

믹스인에서 추상 메서드를 선언하면, 
믹스인을 사용하는 모든 타입이 해당 동작에 따라 달라지는 추상 메서드를 정의해야 합니다.

```dart
mixin Musician {
  void playInstrument(String instrumentName); // 추상 메서드.

  void playPiano() {
    playInstrument('Piano');
  }
  void playFlute() {
    playInstrument('Flute');
  }
}

class Virtuoso with Musician { 
  void playInstrument(String instrumentName) { // 하위 클래스에서 반드시 정의해야 합니다.
    print('Plays the $instrumentName beautifully');
  }  
} 
```

#### 믹스인 서브클래스에서 상태 접근 {:#access-state-in-the-mixins-subclass}

추상 멤버를 선언하면, 
믹스인에서 추상으로 정의된 getters를 호출하여, 
믹스인의 하위 클래스에서 상태에 액세스할 수도 있습니다.

```dart
/// [name] 속성이 있는 모든 타입에 적용할 수 있으며, 
/// 이를 기반으로 [hashCode] 및 `==` 연산자 구현을 제공합니다.
mixin NameIdentity {
  String get name;

  int get hashCode => name.hashCode;
  bool operator ==(other) => other is NameIdentity && name == other.name;
}

class Person with NameIdentity {
  final String name;

  Person(this.name);
}
```

### 인터페이스 구현 {:#implement-an-interface}

믹스인을 추상화로 선언하는 것과 비슷하게, 
실제로 인터페이스를 구현하지 않으면서 mixin에 `implements` 절을 넣으면, 
믹스인에 대한 모든 멤버 종속성이 정의되도록 할 수 있습니다.

```dart
abstract interface class Tuner {
  void tuneInstrument();
}

mixin Guitarist implements Tuner {
  void playSong() {
    tuneInstrument();

    print('Strums guitar majestically.');
  }
}

class PunkRocker with Guitarist {
  void tuneInstrument() {
    print("Don't bother, being out of tune is punk rock.");
  }
}
```

### `on` 절을 사용하여 슈퍼클래스를 선언합니다. {:#use-the-on-clause-to-declare-a-superclass}

`on` 절은 `super` 호출이 해결되는 타입을 정의하기 위해 존재합니다. 
따라서, 믹스인 내부에 `super` 호출이 필요한 경우에만 사용해야 합니다.

`on` 절은 믹스인을 사용하는 모든 클래스가 `on` 절의 타입의 하위 클래스가 되도록 강제합니다. 
믹스인이 슈퍼클래스의 멤버에 종속된 경우, 
이를 통해 믹스인이 사용되는 곳에서 해당 멤버를 사용할 수 있습니다.

```dart
class Musician {
  musicianMethod() {
    print('Playing music!');
  }
}

mixin MusicalPerformer [!on Musician!] {
  performerMethod() {
    print('Performing music!');
    super.musicianMethod();
  }
}

class SingerDancer extends Musician with MusicalPerformer { }

main() {
  SingerDancer().performerMethod();
}
```

이 예에서, 
`Musician` 클래스를 확장하거나 구현하는 클래스만 
`MusicalPerformer` 믹스를 사용할 수 있습니다. 
`SingerDancer`는 `Musician`을 확장하므로, 
`SingerDancer`는 `MusicalPerformer`를 믹스할 수 있습니다.

## `class`, `mixin`, 또는 `mixin class`? {:#class-mixin-or-mixin-class}

:::version-note
`mixin class` 선언에는 최소 3.0의 [언어 버전][language version]이 필요합니다.
:::

`mixin` 선언은 mixin을 정의합니다. 
`class` 선언은 [class][]를 정의합니다. 
`mixin class` 선언은, 동일한 이름과 동일한 타입으로,
일반 클래스와 mixin으로 모두 사용할 수 있는 클래스를 정의합니다.

```dart
mixin class Musician {
  // ...
}

class Novice with Musician { // Musician을 믹스인으로 사용합니다.
  // ...
}

class Novice extends Musician { // Musician을 클래스로 사용합니다.
  // ...
}
```

클래스나 믹스인에 적용되는 모든 제한은 믹스인 클래스에도 적용됩니다.

- 믹스인은 `extends` 또는 `with` 절을 가질 수 없으므로, `mixin class`도 마찬가지입니다.
- 클래스는 `on` 절을 가질 수 없으므로, `mixin class`도 마찬가지입니다.

[language version]: /guides/language/evolution#language-versioning
[class]: /language/classes
[class modifiers]: /language/class-modifiers
