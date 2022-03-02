---
title: "프론트엔드 프로젝트를 배포하는 다양한 방법"
last_modified_at: 2022-01-26T16:20:02-05:00
categories:
  - FrontEnd
tags:
  - tools
toc: true
toc_label: "목차"
---

"프로젝트를 배포한다" 는 말은 실행 가능한 파일을 서버에 올려 사용자가 이용할 수 있게 한다는 뜻이다. 작업한 프로젝트를 github repo에만 올려 놓는다면...

1. git clone
2. npm install
3. (필요하다면) .env 파일 등 사전 환경 설정
4. npm start

...를 해야만 비로소 localhost에서 프로젝트를 확인할 수 있다. 불편함도 불편함이지만, 상황에 따라 설치 과정에서의 오류 때문에 프로젝트를 실행하지 못하는 일도 벌어진다.

이런 문제를 피하기 위해 기업 제출용 과제, 혹은 취업용 포트폴리오 같은 경우 배포를 통해 도메인 입력만으로 프로젝트를 시연해 볼 수 있도록 하는 것이 권장된다.

이 글에서는 원티드 프리온보딩 코스에서 1주차 팀 프로젝트를 진행하면서 직접 체험해 본/알게 된 많은 배포 방법들을 간단히 소개하고, 프로젝트를 직접 배포해 보며 느낀 각 방법의 장단점을 적어 보고자 한다.

## Netlify

![image](https://user-images.githubusercontent.com/28294925/151162541-1913dc7e-c87e-4788-95be-4d7c0e41c366.png)

[공식 문서](https://docs.netlify.com/?_ga=2.190880040.307425302.1643130209-2120768386.1641539853)에 따르면 Netlify는 'all-in-one platform for automating modern web projects'라 한다. 특히 프론트엔드 프로젝트에 특화된 배포 플랫폼이라 할 수 있겠다.

[배포 시 참고한 글](https://jojoldu.tistory.com/546)

### 장점

엄청나게 간단하다. 가입도 하지 않은 상태에서 겨우 오 분 만에 배포를 완료할 수 있을 정도다. HTTPS를 제공하며, github repo와 연결해 CD(Continuous Deployment)를 아주 간단하게 구축할 수 있게 해 주는 것도 큰 장점이었다. 아마 간단한 토이 프로젝트 등을 배포하고자 할 때 최고의 선택이 되지 않을까 한다.

### 단점

https만을 제공한다는 단점이 존재한다. 1주차 팀 프로젝트의 경우 http를 사용하는 서버에서 환율 정보를 받아와야 했기에 충돌이 일어나 결국 netlify로 배포하지 못했다.
또한 편리성이 좋은 만큼 자유도가 떨어지는 편이기에, 캐싱, 최적화 등 직접 설정할 세부사항이 많아질 경우 비교적 사용이 불편해진다는 단점이 존재한다고 한다.

## Heroku

![image](https://user-images.githubusercontent.com/28294925/151168558-8bba161f-4ce0-4bd1-8970-f1d92c618bdd.png)

[공식 문서](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/Welcome.html)에 따르면 Amazon S3는 '데이터를 버킷 내의 객체로 저장하는 객체 스토리지 서비스입니다. 객체는 해당 파일을 설명하는 모든 메타데이터입니다. 버킷은 객체에 대한 컨테이너입니다.'라 한다. 쉽게 말해 거대한 하드디스크라고 생각하면 된다. S3은 단순히 데이터를 저장하는 것 뿐만 아니라 정적 웹 사이트 호스팅 기능도 지원한다. HTML, CSS, JS 파일을 S3에 업로드하면 된다.

[배포 시 참고한 글](https://blog.heroku.com/deploying-react-with-zero-configuration)

### 장점

간단하다.

### 단점

## AWS S3

![image](https://user-images.githubusercontent.com/28294925/151168558-8bba161f-4ce0-4bd1-8970-f1d92c618bdd.png)

[공식 문서](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/Welcome.html)에 따르면 Amazon S3는 '데이터를 버킷 내의 객체로 저장하는 객체 스토리지 서비스입니다. 객체는 해당 파일을 설명하는 모든 메타데이터입니다. 버킷은 객체에 대한 컨테이너입니다.'라 한다. 쉽게 말해 거대한 하드디스크라고 생각하면 된다. S3은 단순히 데이터를 저장하는 것 뿐만 아니라 정적 웹 사이트 호스팅 기능도 지원한다. HTML, CSS, JS 파일을 S3에 업로드하면 된다.

[배포 시 참고한 글](https://42place.innovationacademy.kr/archives/9784)

[배포 시 참고한 또다른 글](https://iborymagic.tistory.com/95)

### 장점

입맛대로 배포 환경을 설정할 수 있다는 점, 또한 수많은 사람들이 사용하고 있는 서비스기에 짧은 구글링만으로도 좋은 참고 자료를 쉽게 찾을 수 있다는 것도 크나큰 장점이다. 직접 체험해 보지는 못했지만, 백엔드 서버로 AWS를 사용할 경우 호환성 역시 좋다고 한다.

### 단점

복잡하고, 공부해야 할 것도 많다. 단순히 build 파일을 집어넣는 것 만으로도 배포가 가능하긴 하지만, 그런 식으로 사용한다면 CD(Continuous Deployment)를 사용할 수 없고, UI가 더 더러운(?) netlify를 사용하는 것과 다를 바가 없다.

## vercel

### 장점

간단하다.

### 단점

## FireBase

참고:

-
