# build.gradle
Gradle을 위한 Groovy문법

# 변수
ex) def: 로컬에서만 접근이 가능하다.
````groovy
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
ex) ext: 프로젝트 전체와 서브 프로젝트에서도 접근이 가능하다.
````groovy
ext {
    springVersion = "3.1.0.RELEASE"
}
````
출처: http://blog-joowon.tistory.com/2 [Joowon's blog]

# sourceSets
##### Package 구조를 구성할때 사용

ex) 
+ src/main/java => src/java: 3단계의 디렉토리를 2단계로 줄임.
+ src/main/resources => src/resouces: 테스트 소스에서 사용할 자원이 없을 것으로 판단해 src/resources 하나로 통합
+ src/test/java => src/test: 3단계의 디렉토리를 2단계로 줄임.
````groovy
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
