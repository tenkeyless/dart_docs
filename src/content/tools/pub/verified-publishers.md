---
# title: Verified publishers
title: 검증된 게시자
# description: Learn what verified publishers are, and they're verified.
description: 검증된 게시자가 무엇인지 알아보고, 그들이 검증을 받았다는 것을 알아보세요.
---

pub.dev 검증된 게시자 배지 <img src="/assets/img/verified-publisher.svg" class="text-icon" alt="pub.dev 검증된 게시자 로고">는 pub.dev 사이트에서 패키지 게시자의 신원을 검증했음을 알려줍니다. 
예를 들어, [dart.dev]({{site.pub}}/publishers/dart.dev/)는 Google의 Dart 팀에서 지원하는 패키지의 검증된 게시자입니다.

이 배지는 pub.dev의 여러 곳에 나타납니다. 검증된 게시자가 게시한 패키지 옆에 있습니다.

* 패키지 검색 결과
* 패키지 세부 정보 페이지
* 게시자 프로필 페이지
* pub.dev 프런트 페이지

각 게시자에는 해당 게시자에 속한 모든 패키지 리스트와 게시자의 연락처 이메일과 같은 추가 세부 정보가 있는 페이지가 있습니다. 
게시자 페이지를 방문하려면, 검증된 게시자 배지 <img
class="text-icon"
  src="/assets/img/verified-publisher.svg" 
  alt="pub.dev verified publisher logo">옆에 있는 게시자 신원 링크(예: `dart.dev`)를 클릭합니다.

## 검증 프로세스 {:#verification-process}

검증된 게시자를 만드는 데 비용이 적게 들고 누구나 사용할 수 있도록, 
pub.dev는 DNS(도메인 이름 시스템) 도메인을 식별 토큰으로 사용합니다. 
많은 패키지 작성자가 이미 신뢰할 수 있는 도메인과 해당 도메인에 대한 홈페이지를 가지고 있기 때문에, 
DNS 검증을 선택했습니다. 
[게시자 생성 프로세스][publishing page] 동안, 
pub.dev는 [Google Search Console][search-console]의 기존 논리에 따라, 
검증된 게시자를 만드는 사용자에게 연관된 ["도메인 속성"][domain-prop]에 대한 
관리자 액세스 권한이 있는지 확인합니다.

## 검증된 게시자 계정 생성 {:#creating-a-verified-publisher-account}

패키지를 게시하고 새로운 검증된 게시자를 생성하려면, [게시 페이지][publishing page]의 지침을 참조하세요.

[domain-prop]: https://support.google.com/webmasters/answer/34592
[publishing page]: /tools/pub/publishing#create-verified-publisher
[search-console]: https://search.google.com/search-console/about
