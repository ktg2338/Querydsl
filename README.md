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

참고: on 절을 활용해 조인 대상을 필터링 할 때, 외부조인이 아니라 내부조인(inner join)을 사용하면,
where 절에서 필터링 하는 것과 기능이 동일하다. 따라서 on 절을 활용한 조인 대상 필터링을 사용할 때,
내부조인 이면 익숙한 where 절로 해결하고, 정말 외부조인이 필요한 경우에만 이 기능을 사용하자.

연관관계 없는 엔티티 외부 조인
주의! 문법을 잘 봐야 한다. leftJoin() 부분에 일반 조인과 다르게 엔티티 하나만 들어간다.
일반조인: leftJoin(member.team, team) 
on조인: from(member).leftJoin(team).on(xxx) 이경우 left table은 from절의 member

JPAExpressions를 통해 서브쿼리를 생성합니다.

from 절의 서브쿼리 한계
JPA JPQL 서브쿼리의 한계점으로 from 절의 서브쿼리(인라인 뷰)는 지원하지 않는다. 당연히 Querydsl
도 지원하지 않는다. 하이버네이트 구현체를 사용하면 select 절의 서브쿼리는 지원한다. Querydsl도
하이버네이트 구현체를 사용하면 select 절의 서브쿼리를 지원한다.

from 절의 서브쿼리 해결방안
1. 서브쿼리를 join으로 변경한다. (가능한 상황도 있고, 불가능한 상황도 있다.)
2. 애플리케이션에서 쿼리를 2번 분리해서 실행한다.
3. nativeSQL을 사용한다. 

case문 => CaseBuilder 사용

상수가 필요한 경우 Expressions.constant(상수)를 사용합니다.

결과에 문자열을 더하고 싶은 경우에 다음 메서드를 사용할 수 있습니다.
concat() : 문자열 뒤에 이어서 더합니다.
prepend() : 문자열의 맨 앞에 더합니다.

프로젝션 대상이 하나면 타입을 명확하게 지정할 수 있음 List<String> result = queryFactory
프로젝션 대상이 둘 이상이면 튜플이나 DTO로 조회 List<Tuple> result = queryFactory

순수 JPA에서 DTO 조회 코드
List<MemberDto> result = em.createQuery(
 "select new study.querydsl.dto.MemberDto(m.username, m.age) " +
 "from Member m", MemberDto.class)
 .getResultList();
순수 JPA에서 DTO를 조회할 때는 new 명령어를 사용해야함
DTO의 package이름을 다 적어줘야해서 지저분함
생성자 방식만 지원함

Querydsl 빈 생성
프로퍼티 접근 - Setter
queryFactory
 .select(Projections.bean(MemberDto.class,
 member.username,
 member.age))
 필드 직접 접근
 .select(Projections.fields(MemberDto.class,
 member.username,
 member.age))
 
프로젝션과 결과 반환 - @QueryProjection
dto class 생성자 + @QueryProjection -> ./gradlew compileQuerydsl-> QMemberDto 생성 확인
이 방법은 컴파일러로 타입을 체크할 수 있으므로 가장 안전한 방법이다. 다만 DTO에 QueryDSL
어노테이션을 유지해야 하는 점과 DTO까지 Q 파일을 생성해야 하는 단점이 있다.

동적 쿼리를 해결하는 두가지 방식
BooleanBuilder
Where 다중 파라미터 사용


