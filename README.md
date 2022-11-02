# Gradle
[Gradle 공식사이트](https://docs.gradle.org/current/userguide/userguide.html)

## io.spring.dependency-management ##
- https://github.com/spring-projects/spring-boot/blob/v2.0.8.RELEASE/spring-boot-project/spring-boot-dependencies/pom.xml

## buildSrc 멀티프로젝트 ##

<!-- 네이버 클라우드에 생성방법 동영상 촬영영상 올려둠 -->

#### STS에서 멀티프로젝트 생성 ###
- Tip: STS에서 멀티프로젝트 생성시 settings.gradle 에 include 프로젝트 설정 한 후, build.gradle에서 sourceSets 작성한 다음, gradle refresh해야 하위 프로젝트가 생성된다.
- gradle refresh 하기전 buildSrc프로젝트 폴더와 build.gradle에 id 'groovy-gradle-plugin' 이 작성되어있어야 한다.
- 만약 buildSrc 폴더 생성전에 gradle refresh를 통한 멀티프로젝트를 생성했다면 groovy-gradle-plugin 인식이 되질 않는다.

1. Spring Starter Project 혹은 New > project > Gradle > Gradle Project 프로젝트를 생성한다.

2. Project Explorer 창을 열고 시작한다.
	- 멀티프로젝트를 하나의 프로젝트 안에서 보여주기때문에 관리가 편하다
	- 기본적으로 프로젝트 관리는 Package Explorer창 에서 관리하는데 Window > Show view > Project Explorer 창을 열고 시작한다.
	![2022-10-31 15 50 48](https://user-images.githubusercontent.com/24876345/198948572-c811ec61-4d7f-4390-be64-1b91a141e302.png)

3. build.gradle 작성
	- sourceSets이 작성돼야 STS에서 프로젝트가 생성된다.
	
	````gradle
	plugins {
		id 'java'
	}


	subprojects {
		apply plugin: 'java'
		    task initSourceFolders {
		    sourceSets*.java.srcDirs*.each {
			if( !it.exists() ) {
			    it.mkdirs()
			}
		    }

		    sourceSets*.resources.srcDirs*.each {
			if( !it.exists() ) {
			    it.mkdirs()
			}
		    }
		}
	}
	````

4. buildSrc 폴더 생성
	- 현재 github buildSrc폴더 및 하위 폴더를 다운로드 한뒤 프로젝트에 COPY한다.

5. 그리고 gradle refresh 한다.

6. buildSrc 아래에 build 폴더가 자동으로 생성된다.

7. gradle 폴더를 제외한 모든 폴더 삭제(파일은 삭제하지 않는다)

## Gradle Wrapper ##
- JAVA 버전상관없이(설치돼있어야함), Gradle 설치없이 빌드가 가능하다.
- gradle.build에 Main class가 설정되어 있어야한다.
````cmd
$ ./gradlew build
````


## Types of plugins ##
- Binary plugins
	+ Plugin 인터페이스를 구현하여 프로그래밍 방식으로 작성하거나 Gradle의 DSL 언어 중 하나를 사용하여 선언적으로 작성할 수 있다.
- Script plugins
 	+ 빌드를 추가로 구성하는 추가 빌드 스크립트로, 일반적으로 빌드를 조작하기 위한 선언적 접근법을 구현한다.

## plugins ##
- 플러그인을 적용하는 최신 방법(v7.5.1)이다.
- 예) io.spring.dependency-management 플러그인을 사용하고 싶다면 아래 사이트에 접속해서 플러그인 존재여부 확인 후 사용가능하다.
- https://plugins.gradle.org/search?term=


- 기본적으로 프로젝트 전체에 적용이고 일부 서브 프로젝트에만 적용하기 위해서 apply false를 선언해 제어하고 사용시 apply plugin으로 명시해 주면 된다.
- 아래는 공식문서의 일부내용이다.

#### Applying external plugins with same version to subprojects ####
> If you have a multi-project build, you probably want to apply plugins to some or all of the subprojects in your build, but not to the root project. 
> The default behavior of the plugins {} block is to immediately resolve and apply the plugins. 
> But, you can use the apply false syntax to tell Gradle not to apply the plugin to the current project and then use the plugins {} block without the version in subprojects' build scripts:

````groovy
plugins {
  id "xyz" version "1.0.0" apply false
}

subprojects { subproject ->
    if (subproject.name == "subPro") {
        apply plugin: 'xyz'
    }
}
````

[출처-Gradle공식문서](https://docs.gradle.org/current/userguide/plugins.html#sec:subprojects_plugins_dsl)


````groovy
// Using Legacy plugin application:
buildscript {
  repositories {
    maven {
      url "https://plugins.gradle.org/m2/"
    }
  }
  dependencies {
    classpath "org.springframework.boot:spring-boot-gradle-plugin:2.0.1.RELEASE"
  }
}

apply plugin: "org.springframework.boot"
````

````groovy
// Using the plugins DSL
plugins {
  id "org.springframework.boot" version "2.7.5"
}
````
[출처](https://plugins.gradle.org/plugin/org.springframework.boot)


## Plain jar vs Executable jar ##
- 스프링 부트 gradle 플러그인 2.5 버전부터 gradle 빌드 시 JAR 파일이 2개 생성된다.
- 별도의 설정을 하지 않았을 때 하나는 [ 프로젝트이름-버전.jar ], 다른 하나는 [ 프로젝트 이름-버전-plain.jar ]이라는 이름을 가진다.

### Plain Archive ##
- -plain이 붙은 jar 파일을 plain archive라고 한다.
- plain archive는 gradle의 jar task로 생성된다.
- plain archive는 어플리케이션 실행에 필요한 모든 의존성을 포함하지 않고, 작성된 소스코드의 클래스 파일과 리소스 파일만 포함한다.
- 이렇게 생성된 jar 파일을 **plain jar**, standard jar, thin jar라고 한다.
- 모든 의존성이 존재하는 게 아니기 때문에 plain jar는 java -jar 명령어로 실행 시 에러가 발생한다. (Manifest 속성이 없다는 오류)

### Executable Archive ##
- -plain 키워드가 없는 jar 파일은 executable archive라고 하며, 어플리케이션 실행에 필요한 모든 의존성을 함께 빌드한다.
- executable acrchive는 gradle의 bootJar task로 생성된다.
- 이렇게 생성된 executable jar는 **fat jar**라고도 한다.
- 모든 의존성을 포함하기 때문에 java -jar 명령어를 통해 실행이 가능하다.
- **Bootjar에 의해 생성된 jar, 문제 없이 실행된다.**

#### 빌드 시 plain jar 생성하지 않도록 설정하기 ####

````gradle
plugins {
	...
}


jar {
	enabled = false
}

````

## Multi Project ##
- 프로젝트를 구성하다보면 공통된 기능과 코드를 하나의 모듈로 몰아놓고 역할에 따라서 각기 다른 모듈에서 참조하여 사용하는 방식을 사용하게 된다. 
- 이는 멀티 프로젝트의 형태를 띄게 된다(사용하는 IDE에 따라서 멀티 프로젝트 혹은 멀티 모듈이라고 부르게 되지만 개념은 동일하다)
- 여러 모듈을 최상위 프로젝트(Root project)에 위치한 빌드스크립트에서 하위 프로젝트를 함께 정의하고 싶어진다.
- 그레이들은 이런 방식을 **Cross project configuration** 이라고 한다.

#### cross project configuration 문제점 ###
> 서브프로젝트의 빌드 스크립트만 봐서는 부모 프로젝트에서 빌드 로직이 주입된다는 것이 분명하게 드러나지 않기 때문에 로직을 파악하기 힘들다.   
> 설정 시점에 프로젝트 간에 커플링이 생기기 때문에 configuration-on-demand와 같은 최적화가 제대로 작동하지 않는다.

#### 권장 방식인 Convention Plugins 방식을 사용 ####

## build.gradle 대신 subproject 이름으로 gradle 파일 구성 ##
- 멀티 모듈 프로젝트를 구성하면 지나치게 많은 build.gradle 파일 때문에 혼란스러워진다.
- 아래와 같이 settings.gradle 에 설정하여 각각의 sub module 들에 대한 설정 파일을 submodule-name.gradle로 변경할 수 있다
````groovy
rootProject.children.each {project ->
    project.buildFileName = "${project.name}.gradle"
}
````
[출처-권남](https://kwonnam.pe.kr/wiki/gradle/multiproject)


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
