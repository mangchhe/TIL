# Multi Module with Gradle

멀티 모듈은 독립적인 프로젝트를 하나의 프로젝트 안의 모듈로서 관리할 수 있는 구조를 제공하는 것을 말한다.

## 장점

- **재사용할 수 있고 서로 독립적이어서 최소한의 의존성을 가지기 때문에 서로에게 영향이 미치지 않아 고려할 필요가 없다.** 예로 KAKAO-API가 필요하다면 프로젝트에 KAKAO-API 모듈만 불러와 사용하면 된다. 만약 KAKAO-API가 싱글 모듈이고 불필요한 기능과 의존성이 있었다면 해당 모듈을 의존성으로 추가하기는 어렵다.
- **변경으로 인한 영향 최소화.** 문제가 발생했을 때 문제가 발생한 모듈만 고쳐서 재배포하면 된다. 그러면 문제가 발생한 부분 외에는 고치고 재빌드하는 동안 계속해서 사용이 가능하다.
- **빌드 속도가 빠르고 유연하다.** 각각의 프로젝트를 빌드할 필요 없이 최상위(루트) 모듈만 빌드하면 모두 빌드할 수 있다. 또한 일부분을 수정했을 때 싱글 모듈이라면 변경되지 않는 부분까지 통째로 재빌드해야 하기 때문에 시간이 많이 소요된다.
- **기능을 업데이트할 때 프로젝트 전체를 이해할 필요 없다.** 업데이트할 기능이 포함된 모듈만 이해하고 있으면 된다.
- **의존성 최소화.** 각각의 모듈을 가볍게 유지해서 빌드 시간을 줄이고 생산성을 향상할 수 있다.

## 단점

- 멀티 모듈에 대한 이해도 & 학습
- 하나로 관리해도 될 것을 여러 개로 쪼개는 것이기 때문에 관리하기 어려울 수 있다.
- 기능이 추가될수록 의존성 관리하기 어렵고 처음 설계한 것과 다르게 동작할 수 있다.

## 실습

- 프로젝트를 하나 만들고 그 아래 세 개의 모듈을 생성
    - 루트 프로젝트 src는 삭제 - 설정 용도로 사용하기 때문에 필요 없음

![multi-module](https://user-images.githubusercontent.com/50051656/170077207-2de7763d-c5a9-43d0-8205-f9415e07d7ed.png)

- 각 모듈은 src 폴더와 build.gradle로 구성

![multi-module2](https://user-images.githubusercontent.com/50051656/170070407-921a1b8c-1abe-4c05-adbf-b1d2631e4836.png)

### settgins.gradle

- 모듈 생성 시 settings.gradle에 각 모듈이 include 됨

```gradle
rootProject.name = 'multi-module'
include 'module-api'
include 'module-web'
include 'module-common'
```

### build.gradle (Root)

- buildscript : 루트를 포함하는 모든 하위 모듈(프로젝트)에 공통으로 구성하는 설정 정보를 설정하는 파일 (Gradle 자체를 위한 설정)
    - ext : 모듈 간 공유할 수 있는 변수
    - repositories : 각종 의존성을 어떤 원격 저장소에서 받을 것인지 설정
        - mavenCentral : 이전부터 많이 사용하는 Gradle 플러그인 저장소, 본인이 만든 라이브러리를 업로드하기 위해서는 많은 과정이 필요 
        - jcenter : 위에 단점을 보완하기 위한 저장소
        - google
    - dependencies : 의존 라이브러리 설정
- allproject : 루트를 포함하는 모든 하위 모듈(프로젝트)에 공통으로 구성하는 설정 정보를 설정하는 파일 (Gradle에서 빌드 중인 모듈을 위한 설정)
- subproject : 루트를 제외하는 모든 하위 모듈(프로젝트)에 공통으로 구성하는 설정 정보를 설정하는 파일
- project('모듈명') : 모듈 설정 정보 설정 (= 각 모듈 build.gradle로도 가능)
    - bootJar.enabled = false, jar.enabled = true 이유는 bootJar는 build 하게 되면 실행할 수 있는 jar 파일로 만들기 위해서 main 함수를 찾는데 필요하지 않아 main 함수가 없는 모듈의 경우에는 찾지 못하고 에러가 나기 때문에 bootJar는 비활성화시키고 jar를 활성화해주는 것이다.
    - common 같은 모듈의 경우 실행할 필요가 없기 때문에 main 함수가 필요하지 않다.

```gradle
buildscript {
    ext {
        spring = 'org.springframework'
        boot = "${spring}.boot"
        bootVersion = "2.5.4"
        lombok = "org.projectlombok:lombok"
    }

    repositories {
        mavenCentral()
    }

    dependencies {
        classpath "$boot:spring-boot-gradle-plugin:$bootVersion"
    }
}

allprojects {
    group = "me.hajoo"
    version = "1.0.0"
}

subprojects {
    apply plugin: "java-library"
    apply plugin: boot
    apply plugin: "io.spring.dependency-management"

    repositories {
        mavenCentral()
    }

    dependencies {
        compileOnly lombok
        annotationProcessor lombok

        testImplementation "$boot:spring-boot-starter-test"
    }

    test {
        useJUnitPlatform()
    }
}

project(':module-common') {
    bootJar.enabled = false
    jar.enabled = true
}
```

# Reference

- [Gradle Reference](https://docs.gradle.org/current/userguide/declaring_repositories.html)
- [우아한형제들 기술 블로그](https://techblog.woowahan.com/2637/)
- [https://dundung.tistory.com/243](https://dundung.tistory.com/243)
- [https://www.youtube.com/watch?v=1ZiBjduthSg](https://www.youtube.com/watch?v=1ZiBjduthSg)