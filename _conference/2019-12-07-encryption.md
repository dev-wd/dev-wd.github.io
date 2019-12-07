---
title: "[릴리쿰 스테이지] 암호가 일치하지 않습니다 - 생활코딩 이고잉님 강의"
date: 2019-12-07 20:26:28 -0400
layout: post
categories: conference
---

회사에서 암호화, 복호화 관련해서 어떤 식으로 해야할지 지식이 전무해서 갈증이 있었다.
조만간 공인인증서도 넣어야하니 말이다..
PM님께서 공유를 해주셔서 신청하여 가게 되었다.

생활코딩은 워낙 유명해서 알고 있었는데, 분야의 특수성 때문인지 실제로 온라인으로도 강의를 들은적은 없었다.

릴리쿰 스테이지라는 홍대입구 쪽에 있는 스테이지에서 진행되었으며 19-22시 총 3시간 동안 진행되었다.

<img src="/images/encryption/en1.jpeg" width="600" height="300">
<img src="/images/encryption/en2.jpeg" width="600" height="300">

삼성역에서 퇴근해서 홍대입구역에서 걸어서 15분은 꾀나 먼 거리라 15분 정도 지각을 하였다.
강의를 할 수 있는 작은 스테이지인것 같았고 카페처럼 음료주문 가능.

<img src="/images/encryption/en4.jpeg" width="600" height="300">

프로그램이 분야가 한정되지 않아서 인상깊었다.

강의를 들은 걸 내용정리를 해서 올려도 된다고 말씀해주셔서 간단하게 정리해보았다.


### 실습 : cyberchef 
  - 암호화 복호화를 연습할 수 있는 툴.

실제로 이 툴을 활용하여 대칭키 알고리즘 실습을 잠깐 진행하였다.
Box 형태로 가져와서 쓸 수 있게 해놓았음


## 대칭키

- 키랑 plain text를 알고를 돌리면 cipher tex가 만들어짐

- 반대로 cipher text data랑 key가 모이면 pain text data 

가장 중요한 것은 key이다. Key는 확실히 숨겨도 되고 cipher 


대칭키 알고리즘에는 여러가지가 있다. 
그중에서 __AES (Advanced Encryption Standard)__ 가 대표적임.
영국의 국정원 gchq에서 만듦.


AES key는 UTF-8로 해야하며, 무조건 16글자여야 한다.
아무리 좋은 방법이더라도 비밀번호 자체가 쉬우면 뚫기 쉬운 방법이 된다.

CBC data가 엄청나게 크면 한번에 암호화를 못해서 쪼개서 block화 해서 암호화를 하는 방법을 뜻한다.


Key를 생성할 때에 input, output 포멧을 선택을 할 수 있는데, raw로 만들면 machine readable language로 변환을 해준다.

사람이 읽기 쉬운 포멧으로 하려면 base64를 쓰자.


- 복호화 과정

역순으로 간다.
base 64이전으로 가니까 



### 번외 : ransomware

랜섬웨어는 컴퓨터에 있는 file을 AES같은걸로 암호화해버림.
그럼 복호화를 해주겠다는 명분으로 돈을 요구함.


## 비대칭키 asymmetric key cryptography

대칭키가 예전에 있었던 방식이면 대칭키가 극복하지 못했던 부분을 비대칭키가 극복함.

key가 두개가 생김. 두개의 키는 짝이다.
public key, private key.
key1으로 암호화 시키면 cipher text는 key2로만 복호화가 된다. key1으론 암호화만 key2로는 복호화만.

key랑 cipher message를 같이 다른 사람에게 배달할 떄 사고가 날 수 있다.
RSA방식으로 이 문제를 해결할 수 있었다.

## RSA
###  public key - cipher

<img src="/images/encryption/en5.jpeg" width="600" height="300">

RSA public key 방식

private key, public key를 만듦

A가 private key public key를 만들고 public key만 B에게 보낸다. public key는 공개되어도 됌. 그러고 B는 A에게 public key와 cipher key를 넘겨준다.


### public key - sign

<img src="/images/encryption/en6.jpeg" width="600" height="300">

전자서명 
A가 보낸 정보를 A가 보냈다고 B가 확신을 할 수 있도록

A 가 key pair 를 만들고 public key를 
(A plain message + cipher message )

a 의 private key로 만든 cipher message가 전자서명이 되는 거임.

B는 cypermessage를  B는 public key로 풀 수 있다.그니까 아까랑 encrypt decrypt 주체가 바뀐거임.


이 방식은 A B 사이에 직접 전달을 하는 경우에만 (또는 신뢰할 수 있을 경우에만 )public key조차 크래커가 속일 수 있긴하다.

이걸 보완한게 공인인증서 이다.

그 시대에 걸맞는 암호의 길이가 있다. 어떤 시대의 컴퓨팅 파워에 따라 달라진다.

RSA도 마찬가지로 Block.


OTP -> one time password
SMS -> 문자 얘 같은 경우에는 되게 위험한 방식이다..
usb로 2차 인증이 가능하다.


### 대칭키을 아직도 쓰는 이유

비대칭키는 성능이 떨어진다.
비대칭키로 아주 자주 일어나는 암호화는 하지 않는다.
대칭키는 성능이 좋음
비대칭키는 배달사고가 안일어남
그래서 두가지 하이브리드해서 쓰는거를 뒤에서 볼 수 있다.


## HASH

단방향 암호화
그냥 복호화 자체가 불가능
이런걸 해시 함수라고 한다.
압축파일을 만든 사람이 저거의 해시방법은 저거야 라고 하면
받고 나면 진짜 저 사람이 만든건지 모르니까

만약 해커가 중간에 나쁜 랜섬웨어를 심었다 하면 해시값이 달라지겠지 
<내가 받은 정보가 안전한가>를 확인 할 때에 사용한다.

쇄도효과  , for 무결성 체크
origin 자체가 진실한지 integrity check 하는 것이다.

### 회원관리

ID , Password 
id 중복 X paassword 노출 X


직원 조차도 password를 알 수 없게 한다.
그래서 hash를 하면 되는거담

시스템에는 해쉬값만 들어있는 거시다.

api내에서 사용자 입력값을 hash해서 맞으면 login을 시켜주면 된다.

중첩적으로 방어수단을 마련하는걸 핵심.

해시는 모든 것에 대한 표를 만들어놓고 가지는 것에 대해서 대응이 되지 않는다.


신뢰기반 - CMS

## REAL WORLD LOGIN SYSTEM

### public key infrastructure 


공인인증서 또는 https 를 사용할 떄 쓴다.
비대칭키는 느리고 많은 데이터를 하기 적합하지 않다.
대칭키는 빠르지만 키 배달 사고가 일어난다.

이 두가지의 장점만 가진 시스템을 가지는게 두가지 다 필요한 것임!!

비대칭키로만 통신하면 너무 느려서 실제 통신은 대칭키로 하고 대칭키는 키가 하나인데 사고가 나서 비대칭키로 키만 배달하고 암호화된 통신은 대칭키로!


A가 pair key를 만들고 A에게 ㅔublic key를 주면 public key로 B의 대칭키를 B가 public key로 암호화 그 Key를 A로 보내준다. 

그 다음부터는 pain message로  message만 A에게 보내준다 그럼 서로 둘다 대칭키를 가지고 있고 public 공간에 key를 놓을 필요가 없으니까 해커는 아무것도 할 수 없다.


### public key - sign
네트워크에서 탈취되는 것에 대해 보호를 하기 위한 작업이다.
A가 pair key를 만들고 public key와 pain message를 40글자 hash로 만들들어 주고 plain text 밑에 hash를 key로 암호화한거를 보낸다. 
B는 public key랑 hash암호화된걸 받는데 B는 이를 public key로 해독해서 자기가 가진 hash랑 같은지 비교한다.


### Certicicate (공인 인증)
우리가 인증해줬다! 하는걸 보증되는 걸 의미한다.
chain으로 대리인이 중간에 끼는 거다.

계약 중간에 위임장이 있는 것이다. chain으로 서로 인증해주는 것.
Trust Chain 이라고 한다.

그래서 사용하는게 PKI 아까 내가 했던 질문에 대한 답편이 이게 될수 있는 것

인증서가 있으면 https 

http로 통신하면 public 누가 볼 수 있음

https 는 양자간 둘다 비대칭키를 가지고 있다. 앞에 말한 방식이 public key infrastructure이게 https 방식인거다 네트워크 내에서도 지원해줄 수 있도록.


2번째 개념이  Certicicate 이다. 
예를 들어 사이트를 똑같이 만들어서 도메인 주소에 살짝 문자 하나를 넣거나 host file을 수정해서 도메인 유아이는 같지만 다른 사이트일 가능성도 있기 떄문에 certicicate이 필요
로그인 했을떄 설정한 그림이 있는거고 

대리인이 중간에 껴있어서 

인증서 보면 발급서가 인증해줬고 그 인증해준곳이 
신뢰가능한 기업인지 봐야한다. 또 그 인증하는 곳 봤는데 root일 때까지 올라가는 것이다.


signiture 에 rsa에 암호화된 정보가 들어있어서 이 정보는 global sign이라는 최상위 인증기간의 public key로만 풀 수 있다. 왜냐면 global signin 의 private key로만 

 chain 인증이라는거다 실제로 쟤가 보증해준거 맞아? 라고 계속 chain을 따라서 묻는거지
 
## 마무리

이고잉님이  오프라인을 무료강의를 하는지 
 처음 알게 되었다. 
 
 대칭키, 비대칭키에 대한 기본 개념은 많이 나와도 실제로 어떻게 적용할까에 대한 고민에 대한 답편을 검색으로 찾기가 힘들었었다.
 
좋은 기회를 통해 real world에서 활용하는 방식을 말씀해주셔서 그 부분이 많이 도움이 되었던 것 같다.



