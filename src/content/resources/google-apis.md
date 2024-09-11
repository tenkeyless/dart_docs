---
# title: Using Google APIs
title: Google APIs 사용
short-title: Google APIs
# description: Your Dart apps can use Firebase and Google client APIs.
description: Dart 앱은 Firebase와 Google 클라이언트 API를 사용할 수 있습니다.
lastVerified: 2021-05-13
---

이 페이지는 Dart 앱에서 [Firebase][] 및 [Google 클라이언트 API][Google client APIs]를 사용하는 데, 
도움이 되는 리소스를 알려줍니다.

## Firebase {:#firebase}

Firebase와 함께 사용하는 Dart API는 Flutter 앱 또는 다른 종류의 Dart 앱에 대한 코드를 작성하는지 여부에 따라 달라집니다.

Flutter 앱은 Analytics, Cloud Firestore, Cloud Functions, Crashlytics와 같은 인기 있는 Firebase 제품에 대해 공식적으로 지원되는 여러 플러그인 중에서 선택할 수 있습니다. 
이러한 플러그인의 전체 리스트는 [FlutterFire][]를 참조하세요.

다른 종류의 Dart 앱은 커뮤니티에서 지원하는 [`firebase` 패키지][`firebase` package]를 사용할 수 있습니다.

## Google 클라이언트 APIs {:#google-client-apis}

[`googleapis` 패키지][`googleapis` package]에는, 
Google Docs API, YouTube Data API, 
Cloud Translation API, Cloud Storage API 등 180개가 넘는, 
Google 클라이언트 API에 대해 생성된 API가 포함되어 있습니다.

Flutter 애플리케이션을 빌드하는 경우, 
[Google API에 대한 Flutter 가이드][flutter-google-apis]를 참조하세요.

서버 애플리케이션의 일부로 Google API를 사용하려면, 
[google_apis 서버 샘플][server-sample]을 참조하세요.

일부 패키지는 `googleapis`에서 제공하는 API에 대한 관용적인 Dart 래퍼를 제공합니다. 
예를 들어, Google Sheets API를 사용하려면, 
[자동으로 생성된 API][gsheets-api-docs-gapi]에 대한 
[대체 API][gsheets-api-docs]를 제공하는 [`gsheets` 패키지][`gsheets` package]를 고려하세요.

Google 클라이언트 API에 대한 래퍼 패키지를 찾으려면, 
[`googleapis`에 종속된 패키지][gapi-packages]를 검색하세요.


[Firebase]: https://firebase.google.com/use-cases
[FlutterFire]: https://firebase.flutter.dev/
[`firebase` package]: {{site.pub-pkg}}/firebase
[gapi-packages]: {{site.pub-pkg}}?q=dependency%3Agoogleapis
[Google client APIs]: https://developers.google.com/api-client-library
[`googleapis` package]: {{site.pub-pkg}}/googleapis
[`gsheets` package]: {{site.pub-pkg}}/gsheets
[gsheets-api-docs]: {{site.pub-api}}/gsheets/latest/gsheets/gsheets-library.html
[gsheets-api-docs-gapi]: {{site.pub-api}}/googleapis/latest/sheets_v4/sheets_v4-library.html
[flutter-google-apis]: {{site.flutter-docs}}/development/data-and-backend/google-apis
[server-sample]: {{site.repo.dart.org}}/samples/tree/main/server/google_apis
