---
# title: Using JSON
title: JSON 사용
# description: Dart solutions for reading and writing JSON.
description: JSON을 읽고 쓰기 위한 Dart 솔루션.
---

대부분의 모바일 및 웹 앱은 웹 서버와 데이터를 교환하는 것과 같은 작업에 JSON을 사용합니다. 
이 페이지에서는 Dart의 JSON _serialization_ 및 _deserialization_ 지원에 대해 설명합니다. 
Dart 객체를 JSON으로 변환하고 JSON에서 변환합니다.


## 라이브러리 {:#libraries}

다음 라이브러리와 패키지는 Dart 플랫폼에서 유용합니다.

* [dart:convert](/libraries/dart-convert)<br>
  JSON과 UTF-8(JSON에 필요한 문자 인코딩)을 위한 변환기입니다.

* [package:json_serializable]({{site.pub-pkg}}/json_serializable)<br>
  사용하기 쉬운 코드 생성 패키지입니다. 
  일부 메타데이터 주석을 추가하고 이 패키지에서 제공하는 빌더를 사용하면, 
  Dart 빌드 시스템이 직렬화 및 역직렬화 코드를 생성합니다.

* [package:built_value]({{site.pub-pkg}}/built_value)<br>
  json_serializable에 대한 강력하고 독선적인(opinionated) 대안입니다.

## Flutter 리소스 {:#flutter-resources}

[JSON 및 직렬화]({{site.flutter-docs}}/development/data-and-backend/json)
: Flutter 앱이 dart:convert와 json_serializable을 모두 사용하여, 
  직렬화 및 역직렬화하는 방법을 보여줍니다.


## 웹 앱 리소스 {:#web-app-resources}

[인터넷에서 데이터 가져오기](/tutorials/server/fetch-data)
: `package:http`를 사용하여 웹 서버로 데이터를 검색하는 방법을 보여줍니다.


{% comment %}
## VM resources

[Write HTTP servers](/tutorials/server/httpserver)
: Walks through how to implement command-line clients and servers
  that exchange JSON data.

## Other tools and resources
{% endcomment %}
