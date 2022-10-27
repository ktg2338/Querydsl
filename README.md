# Querydsl
Querydsl 문법과 순수JPA, DataJPA연동

build.gradle
//querydsl 추가
 implementation 'com.querydsl:querydsl-jpa'

//querydsl 추가 시작
def querydslDir = "$buildDir/generated/querydsl"
querydsl {
 jpa = true
 querydslSourcesDir = querydslDir
}
sourceSets {
 main.java.srcDir querydslDir
}
configurations {
 querydsl.extendsFrom compileClasspath
}
compileQuerydsl {
 options.annotationProcessorPath = configurations.querydsl
}
//querydsl 추가 끝

검증용 Q 타입 생성
Gradle IntelliJ 사용법
Gradle >Tasks >build >clean
Gradle >Tasks >other >compileQuerydsl

Gradle 콘솔 사용법
./gradlew clean compileQuerydsl
Q 타입 생성 확인
build >generated >querydsl
study.querydsl.entity.QHello.java 파일이 생성되어 있어야 함

JPAQueryFactory를 필드로 제공하면 동시성 문제는 어떻게 될까? 동시성 문제는 JPAQueryFactory를
생성할 때 제공하는 EntityManager(em)에 달려있다. 스프링 프레임워크는 여러 쓰레드에서 동시에 같은
EntityManager에 접근해도, 트랜잭션 마다 별도의 영속성 컨텍스트를 제공하기 때문에, 동시성 문제는
걱정하지 않아도 된다.

fetchResults() : 페이징 정보 포함, total count 쿼리 추가 실행

참고: on 절을 활용해 조인 대상을 필터링 할 때, 외부조인이 아니라 내부조인(inner join)을 사용하면,
where 절에서 필터링 하는 것과 기능이 동일하다. 따라서 on 절을 활용한 조인 대상 필터링을 사용할 때,
내부조인 이면 익숙한 where 절로 해결하고, 정말 외부조인이 필요한 경우에만 이 기능을 사용하자.

연관관계 없는 엔티티 외부 조인
주의! 문법을 잘 봐야 한다. leftJoin() 부분에 일반 조인과 다르게 엔티티 하나만 들어간다.
일반조인: leftJoin(member.team, team) 
on조인: from(member).leftJoin(team).on(xxx)

