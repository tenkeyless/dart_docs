---
# title: Using Google Cloud
title: Google Cloud 사용
short-title: Google Cloud
# description: "Your Dart app can use many Google Cloud services: Firebase, Google Cloud Platform, and more."
description: "Dart 앱은 Firebase, Google Cloud Platform 등 다양한 Google Cloud 서비스를 사용할 수 있습니다."
---

Dart 서버는 많은 [Google Cloud 제품](https://cloud.google.com/products)을 사용할 수 있으며, 
종종 사전 패키징된 Docker [Dart 공식 이미지](https://hub.docker.com/_/dart)의 도움을 받습니다.
Dart로 HTTP 서버를 만드는 방법에 대한 자세한 내용은, 
[HTTP 서버 작성 페이지](/tutorials/server/httpserver)를 참조하세요.

Dart 코드에서 사용할 수 있는 다른 Google API(Firebase 포함)에 대한 자세한 내용은, 
[Google API 페이지](/resources/google-apis)를 참조하세요.

## 추천 솔루션 {:#recommended-solutions}

클라우드에서 Dart를 실행하려면, 서버리스 컴퓨팅 솔루션을 사용하는 것이 좋습니다.

### Cloud Run {:#cloud-run}

Cloud Run의 유연한 컨테이너 지원을 Dart의 Docker 이미지와 결합하여, 
서버 측 Dart 코드를 실행할 수 있습니다. 
확장 가능하고 고성능 API와 이벤트 기반 앱을 만드는 것은, 
개발자가 인프라를 관리할 필요가 없는, 
Cloud Run의 서버리스 플랫폼에 대한 좋은 사용 사례입니다.

Cloud Run에서 실행되도록 구현된 Dart 서버의 예는, 
[dart-lang/samples/repo][server examples]에 있습니다.

Cloud Run 사용에 대한 자세한 내용은, 
[다른 언어로 서비스 빌드 및 배포][cr]에 대한 설명서를 참조하세요.

### Dart를 위한 함수 프레임워크 {:#functions-framework-for-dart}

Functions Framework는 웹 요청을 처리하기 위한, 
서버 애플리케이션 대신 Dart 함수를 쉽게 작성할 수 있는, 
FaaS(Function as a Service) 프레임워크입니다. 
이 프레임워크를 사용하면, HTTP 요청과 [CloudEvents][]를 처리하는 함수를 만들어, 
Google Cloud에 배포할 수 있습니다.

[Dart Functions Framework][]는 커뮤니티에서 지원하는 프로젝트입니다.

자세한 내용은 [README][functions docs]를 참조하세요.

## 기타 솔루션 {:#other-solutions}

당신의 요구 사항에 따라, 
다음 Google Cloud 컴퓨팅 플랫폼에서 Dart를 실행하는 것도 고려할 수 있습니다.

### Compute Engine {:#compute-engine}

Compute Engine에서 Dart 코드를 실행하려면, 
Compute Engine의 컨테이너 실행 지원과 Dart의 Docker 이미지를 결합합니다.

자세한 내용은 [소프트웨어 컨테이너 사용][ce]에 대한 Compute Engine 문서를 참조하세요.

### Kubernetes {:#kubernetes}

Compute Engine 인스턴스 클러스터에서 Dart를 실행하려면, 
Google Kubernetes Engine(GKE)을 사용하세요.

자세한 내용은 [GKE 개요][GKE overview]를 참조하세요.

### App Engine {:#app-engine}

[App Engine][]의 Dart 지원은 불완전하며, [App Engine 유연한 환경][App Engine flexible environment]이 필요합니다. 
이 환경은, [제로 인스턴스로 자동 확장][scale to zero]하지 않으므로, 
새로운 서버 측 Dart 코드에는 **Cloud Run**을 권장합니다. 
App Engine을 사용하고 싶다면, [`appengine` 패키지][`appengine` package]를 사용하는 것을 고려하세요.

[App Engine]: https://cloud.google.com/appengine
[App Engine flexible environment]: https://cloud.google.com/appengine/docs/flexible
[scale to zero]: https://cloud.google.com/run/docs/about-instance-autoscaling
[`appengine` package]: {{site.pub-pkg}}/appengine
[ce]: https://cloud.google.com/compute/docs/containers
[cr]: https://cloud.google.com/run/docs/quickstarts/build-and-deploy/other
[server examples]: {{site.repo.dart.org}}/samples/tree/main/server
[GKE overview]: https://cloud.google.com/kubernetes-engine/docs/concepts/kubernetes-engine-overview
[Dart Functions Framework]: {{site.pub-pkg}}/functions_framework
[CloudEvents]: https://cloudevents.io/
[functions docs]: https://github.com/GoogleCloudPlatform/functions-framework-dart/blob/main/docs/README.md
