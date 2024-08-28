## [아이템8] 타입 공간과 값 공간의 심벌 구분하기

- 타입스크립트의 심벌은 타입, 값 공간 둘 중 한곳에 존재한다.
- 타입은 컴파일 하는 과정에서 제거된다.

```
타입-type, interface
값-const, let, var
타입,값-class, 생성자함수, enum
```

클래스 타입공간 => 속성과 메서드가 사용됨
클래스 값 => 생성자가 사용됨

typeof 타입공간=>큰 타입의 일부분으로 사용할 수 있음, type 구문으로 이름을 붙이는 용도로도 사용할 수 있음(❓)
typeof 값 => 자바스크립트 런타임의 typeof 연산자가 됨, 대상 심벌의 런타임 타입을 가리키는 문자열을 반환 이는 타입스크립트의 타입과 다름(자바스크립트 런타임 타입 시스템은 타입 스크립트의 정적 타입스크립트 시스템보다 훨씬 간단함 6개밖에 없기 때문 string, number, boolean, undefined, object, function)

```js
const v = typeof Cylinder; // 값이 function 자바스크립트에서 클래스는 사실상 함수
type T = typeof Cylinder; // 타입이 class Cylinder 생성자 함수 타입
type ClassType = typeof Cylinder;
```

```js
// 1. 클래스 정의
class Cylinder {
    radius = 1;
    height = 1;
}

// 2. 클래스 타입 (typeof 사용)
type ClassType = typeof Cylinder; // 타입은 'class Cylinder'

// 3. 생성자 함수의 타입
type ConstructorType = new () => Cylinder;

// 4. 인스턴스 타입
type InstanceType = Cylinder;

// 5. 클래스 타입을 통해 새로운 인스턴스를 생성할 수 없음 (설명 참고)
// const ll = new ClassType(); // 오류 발생

// 6. 생성자 함수 타입을 통해 새로운 인스턴스를 생성 (정확한 방법)
const c1: InstanceType = new Cylinder(); // 성공
const c2: ConstructorType = Cylinder; // c2는 이제 'new' 키워드로 사용할 수 있음
const c3 = new c2(); // 성공, c3는 Cylinder 인스턴스
```

> 인스턴스 타입, 클래스 타입, 생성자 함수의 타입 이 3개가 헷갈려서 정리해보았습니다

- 클래스의 인스턴스를 타입으로 사용하는 방법 InstanceType

```js
type C = InstanceType<typeof Cylinder>;
```

- 속성 접근자 [] 값 또는 타입으로 사용할 때 동일하게 동작

타입과 값 공간 사이에서 다른 의미를 가지는 코드 패턴

## [아이템9] 타입 단언보다는 타입 선언을 사용하기

타입 선언: 그 값이 선언된 타입임을 명시
타입 단언: 타입스크립트가 추론한 타입이 있더라도 타입단언으로 간주함

타입 단언보다 타입 선언을 사용하는게 좋음 타입 체커가 인터페이스를 만족하는지 검사하지 않고 오류를 무시하기 때문

```js
const pepole = ['hee', 'jinu', 'ruby'].map(name => ({name} as Person));
```

```js
const pepole = ["hee", "jinu", "ruby"].map((name) => (name): Person => ({
  name,
}));
```

> 저는 이런식으로 map함수에 as 키워드를 꽤 썼던 것 같아요 😅 책에 나오는 방식으로 타입 선언해주는것이 더 좋을 것 같다는걸 배웠습니다

- 서브 타입일때 단언이 가능함
- 모든 타입은 unknown 타입의 서브타입

## [아이템10] 래퍼 객체 타입을 피하자

- 불변값인 기본형 string이 메서드를 가지고 있는 것 처럼 보이는 이유는 기본형 string을 String 객체 타입으로 래핑하고 메서드를 호출하고 메서드를 버림
- null과 undefined를 재외하고 모든 원시 타입에 객체 래퍼가 있음
- 타입스크립트에서 래퍼 객체 타입은 지양하고 원시값 타입(기본형 타입)을 사용해야 한다.

## [아이템11] 잉여 속성 체크의 한계 인지하기

- `객체 리터럴`을 할당 할 때, 타입의 속성이 있는지, 타입에 없는 속성이 있는지 검사 (잉여 속성 체크⭕️)

```ts
interface Person {
  name: string;
  age: number;
}

const hee: Person = {
  name: "hee",
  age: 25,
  elephant: true, //오류: Object literal may only specify known properties, and 'elephant' does not exist in type 'Person'.
};
```

- `객체 리터럴`을 제외한 경우 덕타이핑에 의해서 주어진 타입의 속성만 가지고 있는지 검사(잉여 속성 체크❌)

```ts
interface Person {
  name: string;
  age: number;
}

const obj = {
  name: "hee",
  age: 25,
  elephant: true,
};
const p: Person = obj;
```

- 타입이 선택적인 경우 값 타입과 선언 타입에 공통된 속성이 있는지 확인 (공통 속성 체크)
- 잉여 속성 체크는 객체 리터럴 할당시에만 동작하지만, 공통 속성 체크는 약한 타입과 관련된 할당문 마다 수행된다.
