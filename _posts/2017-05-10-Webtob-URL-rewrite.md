WEBTOB URL Rewrite
===========

1. 개요
-------
WEBTOB의 URLRewrite는 URL 을 다루는 강력한 수단을 제공한다. 이것은 원래 아파치 mod_rewrite 의 기능을 재해석하여 구현한 기능이다. 

WEBTOB의 URLRewrite 기능을 처음 다루는 사용자에게 그 사용법과 활용방법에 대해 도움이 되고자 한다.




1. WEBTOB 설정
--------------
WEBTOB 에서 URL Rewrite 설정은 node절이나 vhost절에 설정이 가능하다.

    # *node 절이나 *vhost 절에 설정 예제
    URLRewrite = Y,
    URLRewriteConfig = "/webtob/config/rewrite.conf"
‘rewrite.conf’ 이름은 파일 이름은 임의로 만들어도 상관없음



2. Rewrite.conf 파일 만들기
------------------------


Rewrite config 파일은 크게 2개의 구조로 이루어져 있음.

1. RewriteCond 구조

RewriteCond는 rewriting 조건을 명시하며, 다음과 같은 형식으로 설정한다. 
TestString과 CondPattern을 매칭하여 조건이 맞을 경우, RewriteRule 설정대로 해당되는 패턴을 교체한다.

    RewriteCond <TestString> <CondPattern> [flags]



`<TestString>`에는 예약어와 일반 문자열을 사용 할 수 있는데 자세한 내용은 manual을 참고한다. 대표적으로 host name을 매치 시킬 경우 %{HTTP_HOST} 를 사용한다.

`<CondPattern>` 는 실제로 매칭할 문자를 입력한다.

`[flags]` 다수의 호스트를 매칭하길 원할 경우 OR을 사용해 다음과 같이 설정 할 수 있다.

	RewriteCond %{HTTP_HOST} ^host1.* [OR]
	RewriteCond %{HTTP_HOST} ^host2.* [OR]
	RewriteCond %{HTTP_HOST} ^host3.*
	RewriteRule ...

2.RewriteRule 구조 

사용자 요청이 RewriteCond에 매칭되는 경우, RewriteRule 설정에 의해 사용자 요청 중 Pattern을 Substitution으로 교체한다.

	RewriteRule <Pattern> <Substitution> [flags]

자주 쓰이는 간단한 flags 설명 ( 아래 옵션은 대괄호’[]’ 안에 쓰인다 )

	F     => 403에러 Forbidden페이지로 된다. 
	L     => Last라는 뜻입니다. 위의 Cond들은 여기까지만 적용된다. [L]아래부터는 새로 시작 되는 것. 
	N     => 새로운 Rule이 시작된다는 깃발. 
	QSA  => Cond의 대용을 지난 결과에 덧붙인다. 
	NE    => Out될 값에 특수문자가 HexCode로 되어 포함 되어있는 경우에 사용. 
	R     => Redirection의 뜻이며 어떤 요청이든 무조건 넘긴다. 즉, 뒤 주소로 넘긴다는 뜻 
	NC    => 대소문자 구별 없다는 뜻. 
	OR    => 프로그래밍의 or와 비슷하며, 조건을 중복적으로 정의할 때 사용.


`<Pattern>` **.** 은 요청 uri 전체를 Substitution으로 대체한다.

예를 들어 다음과 같은 상황에서 요청된 www.tmax.com은 www.google.com로 대체된다.

	RewriteCond %{HTTP_HOST} www.tmax.com$ 
	RewriteRule . www.google.com [L]

요청 URI domain:port 인 경우 <Pattern>에 ^/$ 를 통해 “/” 매치해서 redirect 설정을 할 수 있지만 domain:port/uri/ 과 같을 경우 <Pattern>에 “.” 설정하고 뒤에는 Full hostname을 명시해주는 것이 좋다.




3. 주요 활용 예제
-------------------


*가장 쉬운 예로 naver.com 만 입력해도 www.naver.com으로 자동으로 변하듯이 대부분의 포털 사이트의 패턴*


	RewriteEngine On
	RewriteCond %{HTTP_HOST}   !^www [NC]
	RewriteCond %{HTTP_HOST}   !^$
	RewriteRule ^/(.*) http://www.%{HTTP_HOST}/$1 [L,R]



*tmax1.tmax.co.kr로 들어오는 요청을 http://www.naver.com 로 redirect 할 때*

	RewriteCond %{HTTP_HOST} ^tmax1.tmax.co.kr$
	RewriteRule . http://www.naver.com [L]


*tmax1.tmax.co.kr:5200/session/ 요청에 대해 http://www.google.com 로 redirect 할 때*

	RewriteCond %{HTTP_HOST} ^tmax1.tmax.co.kr
	RewriteCond %{SERVER_PORT} 5200$
	RewriteCond %{REQUEST_URI} ^/session/$
	RewriteRule . http://www.google.com [L]


*tmax1.tmax.co.kr:5200/ 으로 들어오는 모든 요청에 대해 tmax1.tmax.co.kr:5200/session/index.jsp로 redirect 할 때*

	RewriteCond %{HTTP_HOST} ^tmax1.tmax.co.kr:5200$
	RewriteRule ^/$ /session/index.jsp [R,L]


*tmax1.tmax.co.kr:5200/session/ 요청에 대해 tmax1.tmax.co.kr:5200/session/index.jsp 로 redirect 할 때*

	RewriteCond %{HTTP_HOST} tmax1.tmax.co.kr:26000$
	RewriteCond %{REQUEST_URI} ^/session/$
	RewriteRule . http://tmax1.tmax.co.kr:26000/session/index.jsp [R,L]
 
*mode라는 변수의 파라미터 값이 test 일 때 www.naver.com 으로 redirect*

	 RewriteEngine On
	 RewriteCond %{QUERY_STRING} ^(.*)mode=test$
	 RewriteRule ^/(.*) http://www.naver.com%{REQUEST_URI} [L,R]

%{REQUEST_URI} : 호스트 부분을 제외하고 디렉토리 부분부터 파라미터까지를 의미.

ex) http://www.sample.co.kr/abc/def.php?param=test 에서 ‘%{REQUEST_URI}’ 부분은 "/abc/def.php?param=test" 임.

*1.1.1.xxx 라는 ip와 1.1.0.xxx 라는 ip 이외의 요청에 대해서 www.naver.com/error/error.html으로 redirect.*

	RewriteCond %{REMOTE_ADDR} 1.1.1.* [OR]
	RewriteCond %{REMOTE_ADDR} 1.1.0.* 
	RewriteCond %{REQUEST_URI} !/error/
	RewriteRule . www.naver.com/error/error.html

!/error/  은 error 라는 요청에 대해서 무한 loop를 방지하기 위해 조건제외를 명시 함.
위의 7번의 설정을 자주 할 수가 있는데 여기서 중요할 것은 대부분의 개발자들이 redirect했을 때 나타나는 html 파일에서 image나 css등을 상대경로로 설정하기 때문에 이를 참고하고 test를 해야 한다.


*도메인 뒤의 URI을 $1, 그 뒤의 경로를 $2로 지정하여 특정 폴더의 내용을 읽게 함.*

프로그램에서 명령어나 내부 code로 쓰이고 있어서 쓰지 못하는 문자를 쓰고 싶을 때. 
(보통 .이 잘못 쓰일것을 대비하여 \.로 쓰는 경우가 있어서 \. 를 예제로 함)
$N(N=1~9)의 경우 현재 처리되고 있는 Rule에서 가리키고 있는 패턴이 그룹으로 묶여 제공된다.
즉, $N을 하나의 변수로 생각하면 된다.

	RewriteCond %{HTTP_HOST} ^[^.]+\.domain\.co\.kr$
	RewriteRule ^(.*?) %{HTTP_HOST}$1 [C]
	RewriteRule ^([^.]+)\.domain\.co\.kr(.*) /home/$1/htdocs$2

위의 것을 한 줄로 쓰면 아래와 같다.

	RewriteRule ^([^.]+)\.domain\.co\.kr(.*) /home/$1/htdocs$2


