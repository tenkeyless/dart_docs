---
# title: "Write HTTP servers"
title: "HTTP 서버 작성"
# description: Communicate over the internet
description: 인터넷을 통해 소통합니다.
toc: false
prevpage:
  url: /tutorials/server/fetch-data
  # title: Fetch data from the internet
  title: 인터넷에서 데이터 가져오기
---

HTTP 서버 작성을 위한 Dart 리소스는 다음과 같습니다.

## 문서 {:#documentation}

* [Google Cloud 사용][Using Google Cloud]에는 Dart 서버에서 사용할 수 있는, 
  Cloud Run과 같은 Google Cloud 제품에 대한 정보가 있습니다.
* [Google API 사용][Using Google APIs]은 Dart 앱에서, 
  Firebase 및 Google 클라이언트 API를 사용하는 데 도움이 되는 리소스를 가리킵니다.

## 샘플 {:#samples}

* [간단한 Dart HTTP 서버][simple-sample]
  * [`shelf`][] 패키지를 사용합니다.
  * [`shelf_router`][] 및 [`shelf_static`][] 패키지도 사용합니다.
  * Cloud Run에 배포할 수 있습니다.
* [Cloud Firestore를 사용하는 Dart HTTP 서버][cloud-sample]
  * [`googleapis`][] 패키지의 Cloud Firestore 기능을 사용합니다.
  * [`googleapis_auth`][], [`shelf`][] 및 [`shelf_router`][] 패키지도 사용합니다.
  * Cloud Run에 배포할 수 있습니다.

[cloud-sample]: {{site.repo.dart.org}}/samples/tree/main/server/google_apis
[`googleapis`]: {{site.pub-pkg}}/googleapis
[`googleapis_auth`]: {{site.pub-pkg}}/googleapis_auth
[`shelf`]: {{site.pub-pkg}}/shelf
[`shelf_router`]: {{site.pub-pkg}}/shelf_router
[`shelf_static`]: {{site.pub-pkg}}/shelf_static
[simple-sample]: {{site.repo.dart.org}}/samples/tree/main/server/simple
[Using Google APIs]: /resources/google-apis
[Using Google Cloud]: /server/google-cloud
