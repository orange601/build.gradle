# build.gradle
Gradle을 위한 Groovy문법

- 로컬 변수 : 'def 변수명' 으로 선언한다.해당 변수는 로컬에서만 접근이 가능하다.
- ext 변수 : 프로젝트 전체와 서브 프로젝트에서도 접근이 가능하다.
출처: http://blog-joowon.tistory.com/2 [Joowon's blog]

ex) def
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

ex) ext
````groovy
ext {
    springVersion = "3.1.0.RELEASE"
    emailNotification = "build@master.org"


}
````

