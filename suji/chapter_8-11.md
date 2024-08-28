# chapter 8
타입(Type): 값이 가질 수 있는 형태

- 변수나 함수가 어떤 종류의 값을 가질 수 있는지, 어떤 작업이 허용되는지 결정
- typescript는 정적 타입 언어로 컴파일될 때 타입이 결정된다
- 타입 오류는 실행전에 코드의 오류를 발견하는데 유용(타입의 안전성 보장)

값(value): 메모리에 실제로 존재하는 데이터

- 값은 변수에 할당되거나 함수의 반환값으로 사용됨

```
// type
interface Cylinder {
	radius: number;
	height: number;
}

// value
const Cylinder = {radius: number, height: number) => ({radius, height});
```

혼란주의: Cylinder 이름은 같지만 타입으로도 사용할 수 있고, 값으로도 사용할 수 있다


# chapter 9
아이템9

변수에 값을 할당하고 타입을 부여하는 방법

1. 타입 선언

선언: 어떤 타입을 가질지 명시

- 타입 검사를 수행할 수 있도록 해줌
- 목적: 타입 안정성 보장
1. 타입 단언

단언: 개발자가 특정 값이 특정 타입임을 보장한다고 컴파일러에게 명시

- 타입스크립트는 단언된 타입을 신뢰
- 개발자가 강제로 타입을 지정하는 것
- 목적: 컴파일러가 추론할 수 없어 오류를 일으킬 만한 경우에 타입을 명확히 알려주는 것

```
const alice: Person = {};
 // error: 'Person' 유형에 필요한 'name' 속성이 없음
const bob = {} as Person;
// 오류 없음
```

- 속성을 추가할 때도 마찬가지이다
    - 선언된 타입에 속성을 추가할 때는 명시된 속성만 지정할 수 있지만
    - 단언된 타입에 속성을 추가할 때는 명시되지 않은 속성을 추가해도 오류가 발생되지 않는다

> 마침 최근에 테스트 코드를 작성하면서 '단언'을 사용해보았어요. 테스트를 할 때, 객체를 실제로 정의하지 않고도 'as Type'으로 강제로 정의하고 로직 테스트에 집중할 수 있어 유용했습니다. 이렇게 테스트 코드에서는 사용할 수 있지만, 실제 작동되는 코드에서는 단언하는 것을 사용하지 않을것 같아요.

# chapter 10 객체 래퍼 타입 피하기
- 불변 기본형: string, number, boolean, null, undefined, symbol, bigint
- String: 객체 / string: 기본형
- 기본형에 메서드를 사용할 때 기본형을 String 객체로 래핑하고, 메서드를 호출하고, 마지막에 래핑한 객체를 버린다
```
x = "hello" // x가 String 객체로 변환
x.language = 'English' // 속성 추가
x.language // = undefined
```
- 속성이 추가된 객체는 버려지기 때문에 undefined
> 이해한 내용: 메서드를 사용하기 위해 기본형을 객체로 잠시 매핑하는 것
- string을 매개변수로 받는 메서드에 String 객체를 전달하면 **문제 발셍**
  - string은 String에 할당할 수 있지만, String은 string에 할당할 수 없기 때문

# chapter 11 잉여 속성 체크의 한계 인지하기
- 잉여속성: 그 외의 속성
- 타입스크립트는 타입이 명시된 변수에 객체 리터럴을 할당할 때 해당 타입의 속성이 있는지, 잉여속성은 없는지 확인
- 객체 리터럴 할당: `변수 = { }`
```
// 객체 리터럴 할당
interface Room {
	numDoors: number;
	ceilingHeightFt: number;
}
const r: Room = {
	numDoors: 1,
	ceilingHeightFt: 10;
	elephant: 'present', // error: 객체 리터럴은 정의된 속성만 지정할 수 있다
}

// 임시변수 사용(구조적 타이핑)
const obj = {
	numDoors: 1,
	ceilingHeightFt: 10;
	elephant: 'present',
}
const r: Room = obj; // 정상
```
- 정의된 객체를 할당하는 것은 가능
  - obj타입은 Room 타입의 부분집함을 포함하기 때문에 타입 체커도 통과

장점
- 구조적 타이핑 시스템에서 허용되는 속성의 이름을 오타강은 실수를 잡는데 효과적인 방법
- 잉여 속성 체크는 일반적인 구조적 할당 가능성 체크와는 역할이 다르다

