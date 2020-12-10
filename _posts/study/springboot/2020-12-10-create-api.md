---
title: "Spring Boot 로 API 생성하기"
categories: [study, springboot]
tag: [create api]
last_modified_at: 2020-12-10
published: false
---
Spring Boot를 이용하여 프로그램을 개발하는 데 있어서 API를 개발하는 것은 당연한 부분입니다. 다른 시스템과의 Communication목적이거나, 혹은 화면(front-end)을 개발하고 그 화면에서 호출할 API를 제공하는 목적으로도 API개발은 필수적입니다. 물론 Spring Boot를 이용하여 스스로 동작하는, 배치 서비스 같은 시스템 개발을 할 수도 있습니다.

사실 제가 앱 개발을 한창 하던 시절에도, 서버 측 개발을 병행하지 않으면 Open API 정도만 사용할 수 있고, 사용자 설정을 저장/백업 하거나, 특별한 기능을 구현하는 등에서 많은 제약이 따라오는 것 같아서 Back-end 개발 쪽으로 관심을 돌리게 된 것도 있습니다. 이제는 Front-end 보다는, Back-end 쪽에 더 가까워져 있습니다만, 이 경우처럼 API 의 개발은 시스템에서 아주 중요한 부분이고, 이 글을 보는 대부분의 사람들이 이러한 목적을 가지고 있을 것입니다. 

예를 들어, 게시판 기능을 가진 시스템이라고 할 경우, 테이블에 저장되어 있는 게시물 목록을 조회하고, 새 글을 작성하고, 글을 수정하고, 글을 삭제하는 등의 일련의 작업들이 다 화면에서 호출하는 API를 통해서 이루어집니다. 물론 JSP 등을 이용해서 API를 사용하지 않고, 화면 내에서 모든 기능들을 다 구현하고 동작하게 할 수도 있겠지만, 확장성이나 개발, 유지보수 측면에서는 별도로 분리하는 것을 추천합니다.

여기서는 API를 개발하고 동작하게 하는 것에 초점을 두고 진행합니다. 실제로 API를 하나 만들게 되면 이정도 수준에서 멈추는 것이 아니라 더욱 많은 작업들이 있겠지만, 그런 부분은 향후에 다른 것들과 더불어서 작업하도록 하겠습니다.

[Download Sample code from GitHub](https://github.com/linkeverything/studySpringBoot/tree/0010-create-api){:target="_blank" .btn .btn--primary}

## 필요한 라이브러리 

## API 설계

## Open API에 대해

## API 병합된 형태

## 참고자료 및 출처

- <https://start.spring.io/>

[^1]: Eclipse 와 다르게, IntelliJ IDEA는 압축 파일의 Import를 제공하지 않습니다. 