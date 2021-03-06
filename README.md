# build.gradle
Gradle을 위한 Groovy문법

# dependency

![img](https://user-images.githubusercontent.com/24876345/96805917-b1071200-144d-11eb-90db-4523c2f42335.png)

## Compile
A라는 모듈을 수정하게 되면, 이 모듈을 직접 혹은 간접 의존하고 있는 B와 C는 모두 재빌드 되어야 한다.
Gradle 3.0부터는 Compile이 deprecated되었다고 한다.

~~(참고로 현재 버전이 4.8.1부터 6.2까지 documentation이 제공되고 있다)~~

## Implementation
A라는 모듈을 수정하게 되면, 이 모듈을 직접 의존하고 있는 B만 재빌드한다.


| 3.0 이전 | 3.0 이후 | 비고 |
|---|:---:|---:|
| `compile ` | implementation |  |
| `testCompile` | testImplmentation |  |
| `debugCompile` | debugImplementation |  |
| `androidTestCompile` | androidTestImplementation |  |


## providedCompile
compile시에는 필요하지만, 배포시에는 제외될 dependency를 설정한다. (war plugin이 설정된 경우에만 사용 가능하다)

## providedRuntime
runtime시에만 필요하고 배포시에는 제외될 dependency를 설정 (war plugin이 설정된 경우에만 사용 가능하다)  

## compile
compile 시점에 필요한 디펜던시 라이브러리들을 compile로 정의

## runtime
런타임 시에 참조할 라이브러리를 정의합니다. 기본적으로 compile 라이브러리를 포함

## testCompile

## compileOnly
컴파일 시점에만 사용하고 런타임에는 필요없는 라이브러리를 정의


# 변수
def: 로컬에서만 접근이 가능하다.
````gradle
def versions = [
	servlet: "4.0.1",
	slf4j: "1.7.25",
	logback: "1.2.3",
	spring: [
		webmvc: "4.3.18.RELEASE"
	],
	jackson: "2.9.6",
	springfox: "2.9.2"
]
````
ext: 프로젝트 전체와 서브 프로젝트에서도 접근이 가능하다.
````gradle
ext {
    springVersion = "3.1.0.RELEASE"
}
````
출처: http://blog-joowon.tistory.com/2 [Joowon's blog]


# buildscript
##### buildscript는 build.gradle 자체를 실행하기 위한 


# sourceSets
##### Package 구조를 구성할때 사용

+ src/main/java => src/java: 3단계의 디렉토리를 2단계로 줄임.
+ src/main/resources => src/resouces: 테스트 소스에서 사용할 자원이 없을 것으로 판단해 src/resources 하나로 통합
+ src/test/java => src/test: 3단계의 디렉토리를 2단계로 줄임.
````gradle
sourceSets {
    main {
        java {
            srcDir 'src/java'
        }
        resources {
            srcDir 'src/resources'
        }
    }
    test {
        java {
            srcDir 'test/java'
        }
    }
}
````

# profile
##### profile이란? 
- 미니 프로젝트가 아닌 이상 대부분의 기업용 서비스는 개발(dev), 테스트(test), 운영(prod) 등으로 구동 환경을 세분화하여 서비스를 관리한다. 이런 식별 키워드를 바로 Profile이라고 부른다. 출처: http://jsonobject.tistory.com/220
##### profile을 사용하는 이유?
- 로컬과 운영 설정파일을 분기한다. (dependency도 분기함)
````gradle
if (!project.hasProperty('profile') || !profile) { 
	ext.profile = 'dev' 
	println "${ext.profile} build start..."
}
````

# 이클립스 환경설정
##### .settings 폴더의 org.eclipse.wst.common.component 파일을 직접 수정함.
##### .classpath 파일을 직접 수정함.
````gradle
eclipse {
    classpath {
        downloadSources = true
        defaultOutputDir = file("WebContent/WEB-INF/classes" )  // 컴파일 후 class 파일이 저장되는 폴더.
      
        // src/test/java, src/test/resources의 output 디렉토리를 지정한다.
        file {
            whenMerged { cp ->
                cp.entries.findAll{ entry ->
                    entry.kind == 'src' && entry.path.startsWith( "src/test/")
                }*.output = "build/test-classes"
            }
        }
    }
    // workspace/{project}/.settings 폴더를 설정한다.
    wtp {
        // .settings 폴더의 org.eclipse.wst.common.component 파일을 설정한다.
        component {
            //contextPath = project.name // 원하는 contextPath 지정. 단, 빈 컨텍스트패스는 "/" 로 지정
            contextPath = "" // 원하는 contextPath 지정. 단, 빈 컨텍스트패스는 "/" 로 지정
        }
        // .settings 폴더의 org.eclipse.wst.common.project.facet.core.xml 파일을 설정한다.
        facet {
            facet name: "jst.web" , version: "2.5" // Servlet Spec Version 지정
            facet name: "jst.java" , version: "1.6" // Java Version 지정, 1.7 ...
            facet name: 'wst.jsdt.web' , version: '1.0'   // Javascript 지정, 1.0
        }
    }
}
````
출처:https://hermeslog.tistory.com/m/270?category=600924


# Gradle Wrapper
##### gradle 을 자동으로 설치해 준다
- 이미 존재하는 프로젝트를 새로운 환경에 설치할때 별도의 설치나 설정과정없이 곧 바로 빌드할 수 있게 하기 위함(Java나 Gradle도 설치할 필요가 없음. 
또한 로컬에 설치된 Gradle 또는 Java의 버전도 신경쓸 필요가 없음. 따라서 항상 Wrapper를 사용할 것을 권장.)
- gradle wrapper 란 녀석을 이용하면, gradle 을 자동으로 설치해 준다. 
이 gradle wrapper(gradlew) 는 windows batch file 또는 shell script 이다. 
이 script 안에 gradle version 이 명시되어 있는데, 명시된 버전을 다운로드 해서 설치 해 준다.
그렇기 때문에 gradle wrapper 를 통해 gradle wrapper 를 만들어 놓으면, 이 project 를 다른 곳에 옮겨가서 다시 build 하기 편리하다.
build.gradle 에 아래처럼 적어놓으면 gradle wrapper 를 이용하게 된다. gradleVersion 이 이용하려는 gradle 의 version 이 된다.

````gradle
task wrapper(type: Wrapper) {
  gradleVersion = '1.0-milestone-3'
}
````
출처 : http://i5on9i.blogspot.com/2013/09/gradle.html

# Create a Jar file with dependencies
##### jar파일에 라이브러리 같이 추가하기
- fatjar를 사용할 필요가 없다. from 부분을 추가하면 된다.
````gradle
version = '1.0.0'
jar {
    baseName = 'myapp'
    version = version
    archiveName = 'myapp.jar'
    manifest {
        attributes 'Main-Class': 'kr.pe.junho85.MyApp',
                   'Implementation-Title': 'myapp',
                   'Implementation-Version': version
    }
    from { configurations.compile.collect { it.isDirectory() ? it : zipTree(it) } }
}
````
출처:https://junho85.pe.kr/380

# buildscript
- 빌드를 하기 위한 스크립트
- 빌드를 할때 외부 라이브러리를 참조해야 할 경우 사용한다.
- dependencies와 다른점은 dependencies는 명시된 상황에서만 의존 라이브러리를 참조한다.
- ringBoot Version 정보, Maven Repository 정보, Dependency 모듈을 지정하여 스프링 부트 플러그인을 사용할 수 있는 기본 바탕을 정의한다.  
- build.gradle 자체를 실행하기 위한 설정이라 보면 된다.

# apply
- apply plugin: 'java' → java용 웹 프로젝트를 생성한다. sourceCompatibility = '1.8' 호환 버전을 지정하여 java 웹 프로젝트에서 사용할 java를 명시한다.
- apply plugin: 'io.spring.dependency-management' → Spring IO Platform의 Gradle Plugin인 dependency-management를 사용한다. 스프링 부트 1.x에서는 디폴트로 사용되었지만 2.x에서는 명시적으로 선언해 주어야 한다.
