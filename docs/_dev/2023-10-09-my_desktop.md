---
layout: post
title: "[바탕화면 정리] 7월 ~ 10월 개발 기간"
categories: [writing]
---

7월 ~ 10월 개발 기간 바탕화면에 급하게 저장된 파일 정리

## 1

<img src='/attachment/231009/0911싱크빅.PNG'>

24번째 줄 즈음 props로 값을 전달했다는 점이 놀라웠던 모양

지금 보면 컴포넌트로 분리하고 싶은 마음 뿐

## 2

<img src='/attachment/231009/10일오류.PNG'>

웹 채팅 시스템 개발할 때.. 말풍선을 불러와 띄우는 것을 모두 클라이언트 js로 구현했다

그때 애를 먹었던 오류였던 모양인데 기억이 안 나네..

## 3

<img src='/attachment/231009/addbodyparserjson.PNG'>

bodyparser 등록 안 해줘서 자꾸 body 데이터 받아오지 못했던 문제!

react랑 node.js를 처음 연결하느라 미숙했던 시절이었나보다

와아 바디파서가 없어서 그랬구나!!! ㅇ0ㅇ 라고 놀라워하며 저장했겠구만

## 4

<img src='/attachment/231009/intpointer01.PNG'>

<img src='/attachment/231009/intpointer02.PNG'>

교수님이 int pointer에 대한 신비한 현상에 대해 설명해 주셨는데 내 gcc 컴파일러는 오류를 뱉어내서 다시 질문 드렸었다

vscode로 코드를 실행하면 a에 b를 더하면 50이 나온다!

아직도 리눅스 gcc에서는 왜 컴파일이 안 되었는지는 의문..

## 5

<img src='/attachment/231009/KakaoTalk_20230822_162217701.png'>

자랑스런 채팅 시스템~

제대로된 첫 개발 프로젝트라서 아주 의미있고 뜻이 깊다

우리 팀 너무 너무 고마워 ~!~!

## 6

<img src='/attachment/231009/mysqlpermissions.PNG'>

<img src='/attachment/231009/mysqlpermissions02.PNG'>

윈도우에 mysql server 설치할 때 윈도우 계정에 권한을 주어야 한다.

## 7

|||
|---|---|
|<img src='/attachment/231009/Screenshot_1695596190.png'>|<img src='/attachment/231009/Screenshot_1695685669.png'>|

각 잡고 제대로 시작한 두 번째 프로젝트!

피로그래밍 모바일 어플 개발하기

## 8

<img src='/attachment/231009/tkdthd.PNG'>

이건 머죠..

## 9

<img src='/attachment/231009/welcometoreactnative.png'>

리액트 네이티브 프로젝트 처음 실행시켰을 때 얼마나 감격스러운지 !!!

## 10

<img src='/attachment/231009/고쳐야해~.png'>

따옴표를 잘 확인하자!

따옴표 안에 따옴표를 써야 한다면 꼭 짝을 맞출 것

## 11

<img src='/attachment/231009/오..개발일지.png'>

음..

## 12

<img src='/attachment/231009/코드스니펫01.PNG'>

<img src='/attachment/231009/코드스니펫02.png'>

역시 채팅 시스템 관련 오류

마크다운 문법을 지원하고자 했는데 줄바꿈 부분에서 어려움을 겪었던 것 같다.

br 태그를 써야 하나.. 줄바꿈 기호로는 안 되나.. 이것 저것 실험해 보았던 흔적인가 ..

채팅 구현 내용을 제대로 정리해 둘 걸!

시간 지나니까 다 잊어버리고 말았다..~!

## 13

ftp 설정 참고 블로그

<https://psychoria.tistory.com/480>

<https://shanepark.tistory.com/12>

## 14

ssl 기억하기!

```
(venv) ubuntu@ip-172-31-34-45:~$ sudo certbot --nginx
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): ywonchae62@gmail.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y
Account registered.

Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: hello.pirot.p-e.kr
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
Requesting a certificate for hello.pirot.p-e.kr

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/hello.pirot.p-e.kr/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/hello.pirot.p-e.kr/privkey.pem
This certificate expires on 2023-11-08.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for hello.pirot.p-e.kr to /etc/nginx/sites-enabled/django-pirot
Congratulations! You have successfully enabled HTTPS on https://hello.pirot.p-e.kr

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

## 15

기억해 놓자~!

```
{% raw %}
{% extends 'base.html' %}
{% load static %}

{% block content %}

<div class="uk-container uk-margin">
    <div class='grid-container'>
        {% for idea in ideas %}
        <div class="card-element">
        <div class="idea-card">
            <div class="img-container">
            {% if idea.image.url != '' %}
            <img src="{{ idea.image.url }}" alt="아이디어이미지">
            {% else %}
            <img src="{% static 'posts/image/no_img.png' %}" alt="대체텍스트" />
            {% endif %}
            </div>
            <h3 class="idea-card-title"><a href="{{ idea.pk }}/detail/">{{ idea.title }}</a></h3>
            <hr class="uk-divider-icon">
            <p>예상 개발 툴 : {{ idea.devtool }}</p>
            <p>아이디어 관심도 : <i class="ri-arrow-up-circle-line arrowup{{ idea.pk }}" id="arrowup{{ idea.pk }}"></i><span class="idea_interest{{ idea.pk }}">{{ idea.interest }}</span><i class="ri-arrow-down-circle-line arrowdown{{ idea.pk }}" id="arrowdown{{ idea.pk }}"></i></p>
        </div>
        </div>
        {% endfor %}
    </div>
    <div class="pagination-container">
        <div class="prev-btn">이전</div>
        <div class="number-btn-wrapper"></div>
        <div class="next-btn">이후</div>
    </div>
</div>

<script src="{% static 'js/pagination.js' %}"></script>
<script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
<script type="text/javascript">
    let pkList = [
        {% for idea in ideas %}
        {{ idea.pk }},
        {% endfor %}
    ];

    function upAndDown(currentPage) {
        for(let i = currentPage * cntLimit; i < currentPage * cntLimit + cntLimit; i++) {
            let pk = pkList[i];
            $('.arrowup'+pk).click(() => {
                $.ajax({
                    type: "POST",
                    url: '/idea/'+pk+'/increase/',
                    data: {'pk': pk, 'csrfmiddlewaretoken': '{{ csrf_token }}'},
                    dataType: "json",
                    success: response => {
                        $(".idea_interest"+pk).text(response.cnt);
                    }
                })
            });

            $('.arrowdown'+pk).click(() => {
                $.ajax({
                    type: "POST",
                    url: '/idea/'+pk+'/decrease/',
                    data: {'pk': pk, 'csrfmiddlewaretoken': '{{ csrf_token }}'},
                    dataType: "json",
                    success: response => {
                        $(".idea_interest"+pk).html(
                        `아이디어 관심도 : <i class="ri-arrow-up-circle-line arrowup{{ idea.pk }}" id="arrowup{{ idea.pk }}"></i>${response.cnt}<i class="ri-arrow-down-circle-line arrowdown{{ idea.pk }}" id="arrowdown{{ idea.pk }}"></i>`
                        );
                    }
                })
            });
        }
    }

    upAndDown(currentPage);
</script>
{% endblock %}
{% endraw %}
```

## 16

꼭!!

<https://stackoverflow.com/questions/40453881/change-of-opacity-using-css-transition-and-vanilla-javascript-works-only-when-fa>

포스트 쓰기 transition꼭..

transition은 hover와 같은 액션에 지정하면 제대로 동작하지 않는다.

원형(?)에게 등록할 것

## 17

기록했던 채팅 말풍선 로드 알고리즘

첫 번째 말풍선은 무조건 시간 표시

두 번째 말풍선(다음 말풍선)과 비교해서 noti가 아니고, 시간이 같으면 프로필 표시 x -> 같은 함수 b

두 번째 말풍선부터는 첫 번째 말풍선(이전 말풍선)과 세 번째 말풍선(다음 말풍선)과 비교가 필요

이전 말풍선이 noti가 아니고, 시간이 같으면 시간표시 x -> 같은 함수 a

다음 말풍선이 noti가 아니고, 시간이 같으면 프로필 표시 x -> 같은 함수 b

noti가 아닌 마지막 말풍선은 무조건 프로필 표시

마지막 말풍선은 마지막에서 첫 번째 말풍선(이전 말풍선)과 비교가 필요 -> 같은 함수 b

이전 말풍선이 noti가 아니고, 시간이 같으면 시간표시 x -> 같은 함수 a

## 18

서버 해킹하기

nmap

Nikito

## 19

장고 배포 시 사용한 여러가지 명령어들

gunicorn --bind gunicorn.sock config.wsgi:application -c conf.d/gunicorn.conf.py

uwsgi --http :8000 --home /home/ywonchae1/django-pirot/venv/ --chdir /home/ywonchae1/django-pirot/server/ --module config.wsgi

gunicorn -e DJANGO_SETTINGS_MODULE=config.settings config.asgi:application -c conf.d/gunicorn.conf.py --preload

## 20

코드스니펫 관련..

```
"<h2>this is code snippets</h2>
<p><br></p>
<div class="codehilite"><pre><span></span><code><span class="k">def</span> <span class="nf">foo</span><span class="p">(</span><span class="n">request</span><span class="p">):</span><br>   <span class="k">if</span> <span class="n">request</span><span class="o">.</span><span class="n">method</span> <span class="o">==</span> <span class="s1">&#39;POST&#39;</span><span class="p">:</span><br>      <span class="nb">print</span><span class="p">(</span><span class="s1">&#39;hello world~&#39;</span><span class="p">)</span><br><br><span class="k">while</span><span class="p">(</span><span class="n">true</span><span class="p">):</span><br>    <span class="nb">print</span><span class="p">(</span><span class="s1">&#39;piro jjang&#39;</span><span class="p">)</span><br></code></pre></div>"

"<h2>this is code snippets</h2>
<div class="codehilite"><pre><span></span><code><span class="k">def</span> <span class="nf">foo</span><span class="p">(</span><span class="n">request</span><span class="p">):</span>
   <span class="k">if</span> <span class="n">request</span><span class="o">.</span><span class="n">method</span> <span class="o">==</span> <span class="s1">&#39;POST&#39;</span><span class="p">:</span>
      <span class="nb">print</span><span class="p">(</span><span class="s1">&#39;hello world~&#39;</span><span class="p">)</span>

<span class="k">while</span><span class="p">(</span><span class="n">true</span><span class="p">):</span>
    <span class="nb">print</span><span class="p">(</span><span class="s1">&#39;piro jjang&#39;</span><span class="p">)</span>
</code></pre></div>"
```

## 21

검색 관련

```py
def isHangul(ch): #주어진 문자가 한글인지 아닌지 리턴해주는 함수
    JAMO_START_LETTER = ord(u'가')
    JAMO_END_LETTER = ord(u'힣')
    return ord(ch) >= JAMO_START_LETTER and ord(ch) <= JAMO_END_LETTER

def isChosung(ch):
    # 주어진 문자가 종성인지 아닌지 리턴
    JONGSUNG_START_LETTER = ord('ㄱ')
    JONGSUNG_END_LETTER = ord('ㅎ')
    return ord(ch) >= JONGSUNG_START_LETTER and ord(ch) <= JONGSUNG_END_LETTER

#Constants
CHOSUNG_START_LETTER = 4352
JAMO_START_LETTER = ord(u'가')
JAMO_END_LETTER = ord(u'힣')
JAMO_CYCLE = ord(u'까') - ord(u'가')
CHOSUNG_BETWEEN_JONGSUNG = ord('ㄱ') - 4352

print(CHOSUNG_BETWEEN_JONGSUNG)

text = "대한민국 ㅁ세가"
for ch in text:
    if isHangul(ch): #한글인 경우에만 초성을 추출하고 그렇지 않은 경우엔 문자를 그대로 출력합니다.
        print(chr((ord(ch) - JAMO_START_LETTER)//JAMO_CYCLE + CHOSUNG_START_LETTER))
    elif isChosung(ch):
        print(ord(ch) - CHOSUNG_BETWEEN_JONGSUNG)
    else:
        print(ch)

print(ord('ㄱ'), ord('가'), chr(4352), ord('ㄷ'))

test = 'hello'
print(test)
test += 'p'
print(test)
```

## 22

암호화 관련

```py
import base64
import hashlib
from Crypto.Cipher import AES # 대칭키를 사용하기 위한 모듈 임포트

BS = 16 # blocksize를 16바이트로 고정시켜야 함(AES의 특징)

# AES에서는 블럭사이즈가 128bit 즉 16byte로 고정되어 있어야 하므로 문자열을 encrypt()함수 인자로 전달시
# 입력 받은 데이터의 길이가 블럭사이즈의 배수가 아닐때 아래와 같이 패딩을 해주어야 한다.
# 패딩: 데이터의 길이가 블럭사이즈의 배수가 아닐때 마지막 블록값을 추가해 블록사이즈의 배수로 맞추어 주는 행위
pad = (lambda s: s+ (BS - len(s) % BS) * chr(BS - len(s) % BS).encode())
unpad = (lambda s: s[:-ord(s[len(s)-1:])])

class AESCipher(object):
    def encrypt(self, message): # 암호화 함수
        message = message.encode() # 문자열 인코딩
        raw = pad(message) # 인코딩된 문자열을 패딩처리
        cipher = AES.new(self.key, AES.MODE_CBC, self.__iv().encode('utf8')) # AES 암호화 알고리즘 처리(한글처리를 위해 encode('utf8') 적용)
        enc = cipher.encrypt(raw) # 패딩된 문자열을 AES 알고리즘으로 암호화
        return base64.b64encode(enc).decode('utf-8') # 암호화된 문자열을 base64 인코딩 후 리턴

    def decrypt(self, enc): # 복호화 함수 -> 암호화의 역순으로 진행
        enc = base64.b64decode(enc) # 암호화된 문자열을 base64 디코딩 후
        cipher = AES.new(self.key, AES.MODE_CBC, self.__iv().encode('utf8')) # AES암호화 알고리즘 처리(한글처리를 위해 encode('utf8') 적용)
        dec = cipher.decrypt(enc) # base64 디코딩된 암호화 문자열을 복호화
        return unpad(dec).decode('utf-8') # 복호화된 문자열에서 패딩처리를 풀고(unpading) 리턴
    
    def __iv(self):
        return chr(0) * 16
    
    def __init__(self, key):
        self.key = hashlib.sha256(key.encode()).digest() # 키가 쉽게 노출되는 것을 막기 위해 키를 어렵게 처리하는 과정으로 보통 해시를 적용
        print("AES Key(Key문장 암호화) : ", self.key)


print("-"*100, "\n")
key = "aesKey"
msg = "원본 메시지 입니다."
print("AES KEY: ", key)
print("원본 메시지: ", msg)


aes = AESCipher(key) # 1. 대칭키 암복호화 처리를 위해 AESCipher클래스의 객체(인스턴스)를 생성(해시(256bit)가 적용된 키값을 얻어옴)
print(aes)

encrypt = aes.encrypt(msg) # 2.입력한 메시지를 AES 대칭키 암호화 방식으로 암호화
print()
print("원본 메시지를 AES키로 암호화한 결과: ", encrypt)

decrypt = aes.decrypt(encrypt) # 3.암호화된 메시지를 AES 대칭키 암호화 방식으로 복호화
print()
print("암호화된 메시지를 복호화한 결과: ", decrypt)
```

```py
from cryptography.fernet import Fernet

def generate_key():
    return Fernet.generate_key()

def encrypt(text, key):
    fernet = Fernet(key)
    encoded_text = text.encode('utf-8')
    encrypted_text = fernet.encrypt(encoded_text)
    return encrypted_text

def decrypt(encrypted_text, key):
    fernet = Fernet(key)
    decrypted_text = fernet.decrypt(encrypted_text).decode('utf-8')
    return decrypted_text

# 예시
key = generate_key()
text = "안녕하세요, Hello!"

encrypted_text = encrypt(text, key)
decrypted_text = decrypt(encrypted_text, key)

print("원본 텍스트:", text)
print("암호화된 텍스트:", encrypted_text)
print("복호화된 텍스트:", decrypted_text)
```