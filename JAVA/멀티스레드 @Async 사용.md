
# 멀티 스레드 @Async 사용기

## 사용계기
- 필자가 AWS sdk를 활용하여 각 리소스의 데이터를 가져와 대시보드에 보여주는 API를 개발
- 각각의 모듈을 불러오는 중, 속도 저하 문제 발생
- 멀티스레드로 구현을 위해 리서치 중 @Async 확인

## 개념
- Spring 에서 쓰레드 풀을 활용한 비동기 메소드를 지원
- 메소드에 @Async를 달아두면 비동기로 리턴하고 spring TaskExecutor로 새로운 스레드 실행

## 사용법

## 실제 적용 사례

## TroubleShooting

## 참고 링크
- 구현 시 참고한 링크 : https://cano721.tistory.com/208#Return_%EA%B0%92%EC%9D%B4_%ED%95%84%EC%9A%94%ED%95%A0%EB%95%8C
