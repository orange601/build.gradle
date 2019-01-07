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
- Package를 구조를 구성할때 사용
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
