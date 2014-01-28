# PLAY

## 특징
* lightweight
* stateless (무상태)
* web-friendly 아키텍처
* iteratee IO 기반의 [reactivate programming](http://mobicon.tistory.com/294) ,  확장성 높은 예측 가능한 소량의 리소스 소비(cpu, 메모리, 스레드)

# 1.설치

## 준비
* jdk 6 이상 (7 버전은 최신버전을 받아라 )
* 맥의 경우는 homebrew 로 jdk 나 play 패키지를 관리하는게 훨씬 나을것이다.

## 패키지 종류
패키지는 크게 2가지가 있다. 

* Typesafe Activator : Typesafe Activator 를 이용해서 설치하는 방법. 일종의 웹 기반 개발 IDE 라고 봐도 된다. cloud9 이나 nitro.us 같은 사이트에서 개발해봤다면 무슨의미인지 알 것이다.
* Play standalone : 말그대로 독립적인 플레이 패키지라고 봐도 된다. 

## path 추가함 
* 패스를 정의해둔다. ex) export PATH=$PATH:/relativePath/to/play  
* 참고로 프록시를 이용중이라면 set HTTP_PROXY=http://<host>:<port>(윈도우)  export HTTP_PROXY=http://<host>:<port> (유닉스류) 의 설정이 필요할 수 있다.

## 플레이 설치 확인
간단히 play help 를 쳐서 확인할 수 있다. 
![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/play.png)

# 2. 어플리케이션 생성
여기서는 모두 standalone 기반으로 진행할 것이다. 

## standalone 에서의 프로젝트 생성
생성은 간단하다

$ play new 프로젝트이름
![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/playNew.png)

* 이름과 어플리케이션 템플릿을 뭘쓸질 정한다.
> 참고로 여기서 언어템플릿을 정했다고 해서 후에 변경할 수 없는것은 아니다. 후에 다른 언어코드를 추가 할 수 있다.

실행방법은 간단하다. 새로 만든 어플리케이션이름의 디렉토리에 들어가서 __play__ 라고 실행하면 된다.

## play 설치없이 어플리케이션 생성하기
[sbt](https://github.com/codeport/scala/wiki/SBT시작하기) 가 필요하다.  

새 어플리케이션을 위한 디렉토리를 만들고 sbt 빌드 스크립트 `project/plugins.sbt` 에 2가지 내용을 추가하면된다.

    //The Typesafe repository
    resolvers += "Typesafe repository" at "http://repo.typesafe.com/typesafe/releases/"
        
    // Use the Play sbt plugin for Play projects
	addSbtPlugin("com.typesafe.play" % "sbt-plugin" % "2.2.x")	
        
snapshot 버전을 쓰고 싶으면 설정을 바꾸면 된다.

	// Typesafe snapshots
	resolvers += "Typesafe Snapshots" at "http://repo.typesafe.com/typesafe/snapshots/"

build.sbt 에는 

	import play.Project._
	name := "My first application"
	version := "1.0"
	
모든 설정후에 이제 디렉토리로 들어가 sbt 실행하면 된다. 그러면 프로젝트를 로드하고 

# 3. Play 어플리케이션 해부 
## 기본 어플리케이션 레이아웃 

	app                      → Application sources
	 └ assets                → Compiled asset sources
	    └ stylesheets        → Typically LESS CSS sources
	    └ javascripts        → Typically CoffeeScript sources
	 └ controllers           → Application controllers
	 └ models                → Application business layer
	 └ views                 → Templates
	build.sbt                → Application build script
	conf                     → Configurations files and other non-compiled resources (on classpath)
	 └ application.conf      → Main configuration file
	 └ routes                → Routes definition
	public                   → Public assets
	 └ stylesheets           → CSS files
	 └ javascripts           → Javascript files
	 └ images                → Image files
	project                  → sbt configuration files
	 └ build.properties      → Marker for sbt project
	 └ plugins.sbt           → sbt plugins including the declaration for Play itself
	lib                      → Unmanaged libraries dependencies
	logs                     → Standard logs folder
	 └ application.log       → Default log file
	target                   → Generated stuff
	 └ scala-2.10.0            
	    └ cache              
	    └ classes            → Compiled class files
	    └ classes_managed    → Managed class files (templates, ...)
	    └ resource_managed   → Managed resources (less, ...)
	    └ src_managed        → Generated sources (templates, ...)
	test                     → source folder for unit or functional tests	
	
## app/ 
모든 실행가능한 것들(자바,스칼라 소스와 테플릿, 컴파일된 자원들)을 담고 있다. 

app 에는 MVC 아키텍처 패턴의 구성인 3가지 기본 패키지가 있다.

* controllers
* models
* views

물론 app 이하에 각자의 패키지를 만들어도 된다. 
> 참고로 play 안의 위의 3가지 패키지 이름은 필요에 따라 바꿀 수도 있다. 

## public/
웹서버에서 직접 제공될 리소스들의 저장위치 

이미지,CSS,자바스크립트등 3개의 서브디렉토리들로 나뉘어진다.
> 근래에 만들어지는 어플리케이션은 /public 이 /assets 로 맵핑되나 쉽게 수정할 수 있고, 여러개의 디렉토리도 쓸 수 있다. 

## conf/
어플리케이션의 설정을 담고 있다. 거기에는 2개의 메인이 되는 설정파일이 있다.

* `application.conf` 는 기본설정과 파라미터등 어플리케이션의 중심이 되는 설정파일이다. 
* `routes`  파일은 경로에 대한 정의 파일이다.

설정옵션들에 대해 추가하고 싶다면 `aoolication.conf` 파일에 추가하면 된다. 

만약 라이브러리가 특정 설정이 필요하다면, `conf` 디렉토리 아래에 파일을 설정해보도록 한다.

## lib/ 
빌드 시스템이 아닌 직접 관리하는 jar 같은 의존성 라이브러리들이 담겨진 디렉토리이다.(옵션) 어플리케이션 클래스패스에 연결하고 jar 를 여기 놓기만 하면된다.

## build.sbt
작성한 메인 빌드 정의는 보통 프로젝트 root 에 있는 `build.sbt` 에서 찾을 수 있다.  `project/` 안에 `.scala` 파일또한 프로젝트 빌드에 이용된다.

## project/
sbt 빌드 정의를 담고 있다.

* `plugins.sbt` 는 프로젝트에서 사용되는 sbt 플러그인을 정의한다.
* `build.properties` 는 프로젝트 빌드에 사용하는  sbt 버전정보를 담고 있다.

## target/
빌드에 의해 생성되는 모든것들을 담고 있다. 이곳에 무엇이 생성되는지 아는것은 도움이 된다.

* `classes/` 는 (자바/스칼라) 컴파일된 모든 파일들이 담겨 있다.
* `classes_managed` 는 프레임웍에 의해 관리되는 클래스들만 담겨 있다. (router 또는 템플릿 시스템에 의해 생성된 클래스들) 이용하는 IDE 의 외부 클래스폴더로 추가 하는것이 유용할 것이다. 
* `resource_managed` 는 컴파일된 LESS CSS 나 커피스크립트 내용들을 내용같은 생성된 리소스를 담고 있다.
* `src_managed` 템플릿 시스템에 의해 생성된 스칼라 소스들을 담고 있다.

## .gitignore 파일
생성된 폴더들은 이용하는 버전관리시스템에 의해서 무시된다. 플레이 어플리케이션을 위한 일반적인 `.gitignore` 유형은 아래와 같다

	logs
	project/project
	project/target
	target
	tmp
	dist
	.cache

# 4. Play console 이용
## console 실행 
play 콘솔은 Play 어플리케이션의 완벽한 개발환경을 관리하게 해주는 sbt 를 기반으로한 개발 콘솔이다.

### Typesafe Acrivator 를 이용한 콘솔실행
UI 기반의 빌트브라저를 이용하는 Typesafe Activator 를 이용해 Play 개발 환경을 띄울때 선호되는 방식이다.
그러나 기본 환경의 콘솔을 이용하는걸 선호한다면 어플리케이션 디렉토리를 바꾸고 `activator` 를 실행시켜라.

### Play standalone 콘솔을 이용한 실행
만약 standalone 버전을 설치했다면, 존재하는 play 어플리케이션 디렉토리로 이동해서 `play` 를 실행하라.

![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/console.png)

## Getting help
기본적인 명령어들에 대한 help 를 보고 싶다면

	$ help play 
	
하면된다. 

## 개발모드로 서버 실행하기
개발모드로 어플리케이션을 실행하고 싶으면, `run` 을 입력한다.

![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/consoleRun.png)

개발 모드에서는 auto-reload 설정이 활성화되어 실행된다. 즉 매번의 리퀘스트 마다 Play 는 프로젝트 전체를 체크하여 요청된 리소스를 재컴파일한다.
필요하다면 어플리케이션을 재시작할 수 도 있다. 

만약 어떠한 에러라도 있다면, 브라우저에 바로 결과가 표시되는걸 볼 수 있다.

![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/errorPage.png)

서버를 중지시기려면 Ctrl + D 하면 된다.

## Compiling
Play 에서는 서버의 실행없이도 어플리케이션을 컴파일 할 수 있다. `compile` 이라고 입력하면 된다.	

![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/consoleCompile.png)

## interactive 콘솔 실행 
interactive 형 스칼라 콘솔에 들어가려면 `console` 을 입력한다. 이는 코드를 interactive 형태로 테스트 할 수 있도록 해준다.

scala 콘솔에서 어플리케이션을 실행하기 위해서는

`bash scala> new play.core.StaticApplication(new java.io.File("."))`

![](http://www.playframework.com/documentation/2.2.x/resources/manual/gettingStarted/images/consoleEval.png)

## Debugging
콘솔이 시작할때 Play 에게 [JPDA](http://docs.oracle.com/javase/7/docs/technotes/guides/jpda/) 디버그 포트를 열도록 요청할 수 있다. 그러면 자바디버거를 이용해서 접속할 수 있다. `play debug` 명령을 이용한다.

JPDA 포트가 열렸다면 boot 동안 아래의 로그를 보여줄 것이다.

`Listening for transport dt_socket at address: 9999`

> `play debug` 를 통한 JPDA 소켓은 9999 가 열릴것이다. 물론 `set JPDA_PORT=1234` 처럼 `JPDA_PORT` 를 환경설정에 정의할 수 도 있다.

## sbt 특징 사용
Play 콘솔은 보통의 sbt 콘솔이다. 해서 __triggered execution__ 같은 sbt 특징들을 이용할 수 있다.

예로 `~ compile` 를 이용하면 

`[your application] $ ~ compile` 

소스를 바꿀때 마다 적용한것이 동작할 것이다.

`~ run` 을 해놨다면?

개발 서버가 실행되는 동안 트리거된 정의들이 enable 될 것이다. 

또한 `~ test` 해두면 소수가 수정되는 매번 프로젝트를 테스트 하게 될 것이다. (_junit 같은걸 의미하는게 아닐까?_)

## 직접적인 play 명령들
Play 콘솔에 들어가지 않고도 명령들을 바로 실행할 수 있다. 예로 `play run` 을 넣으면

	$ play run
	[info] Loading project definition from myFirstApp/project
	[info] Set current project to My first application

	--- (Running the application from SBT, auto-reloading is enabled) ---

	[info] play - Listening for HTTP on port 9000...

	(Server started, use Ctrl+D to stop and go back to the console...)
	
어플리케이션이 바로 실행될 것이다. `ctrl+d` 를 하면 다시 OS 프롬프트로 돌아올것이다.

## Force clean
이상이 있거나 sbt 캐시가 문제를 일으켰다고 생각되면 `clean-all` 명령을 통해 모든 생성된 디렉토리를 claen 할 수 있다.

`play clean-all`	

# 5. 선호하는 IDE 설정
Eclipse, Intellij, Netbeans, ENSIME 등 여러가지가 소개되어있으나 intelliJ 하나만 보겠다.

설정방법은 간단하다. play 콘솔에서 `idea` 라는 명령만 넣으면 된다. 좀더 자세히 보면

jars 소스 없이 생성시

`$ idea`

jars 소스까지 같이 생성한다면 (시간이 더 걸릴 수 있다. 그리고 몇몇 소스는 없을 수 있음)

`$ idea with-sources=yes`

이렇게 하면 play 어플리케이션을 project 로 열기위해 필요한 IntelliJ  설정파일들이 생성될것이다. 파일은 `.iml` 파일과 `-build.xml` 파일이다. 

IntelliJ 를 실행해서 open 으로 해당 프로젝트 폴더를 선택하면 된다.

> 자바 기반의 Play 2 어플리케이션이 Scala 플러그인을 가졌을때 빌딩시 문제가 있다.  그전까지 scala 플러그인을 비활성화 처리해놓기를 권장한다.

이제 디버그 모드에서 실행해보자. 이미 실행한 play 콘솔은 stop 해야한다. exit 로 빠져나온다.

Debug를 위해서는 우선 debug 설정을 추가한다.

1. Run/Debug 설정 다이얼로그를 열고, Run -> Edit Configurations 를 선택한다.
2. Remote 설정을 추가하고 Remote 를 선택하자.
3. 다음을 설정하라
	* 이름
	* Transport : Socket
	* Debugger mode : Attach
	* Host : localhost
	* Port : 9999
	* import한 모듈을 고른다.
4. 적용하고 창을 닫는다.

Play 를 debug 모드로 실행하자. (IDEA 안에서가 아니라, 터미널에서)

`$ play debug`

아래의 내용이 나올것이다.

`Listening for transport dt_socket at address: 9999`

몇몇 브레이크 포인트를 설정하고 IDEA 에서 새로운 디버그 설정으로 실행하면 콘솔에는 아래의 내용이 나온다.

`Connected to the target VM, address: 'localhost:9999', transport: 'socket'`

play 콘솔에서 `run` 을 통해 web app 을 실행하면 브라우저에 `http://localhost:9000` 이 나올것이다. 그리고 intelliJ는 breakpoint 에서 멈출것이다.

대신에, 더 많은 명령 프롬프트를 실행되지 않게 하기 위해서는 IDEA 의 play 콘솔에 `play debug run` 를 실행한 다음 debug 설정을 실행하라. 

만약 어플리케이션에 어떤 중요한 변화가 생기면,(예로 클래스 패스) `idea` 명령으로 설정파일들이 다시 생성되도록 하라. 

