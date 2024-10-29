## [아이템12] 함수 표현식에 타입 적용하기

- 타입스크립트에서는 재사용이 가능하기 때문에 문장보다는 함수 표현식을 사용하는 편이 좋다.
- 반복되는 함수 시그니처를 하나의 함수 타입으로 통합 할 수 있다.
- 함수 구현부도 분리되어 있어 로직이 분명해짐
- 라이브러리를 직접 만든다면 공통 콜백에 타입을 제공해야한다.

```ts
//1. 함수 문장
function add(a: number, b: number) {
  return a + b;
}
function sub(a: number, b: number) {
  return a - b;
}

//2. 함수 표현식
type BinaryFn = (a: number, b: number) => number;
const add: BinaryFn = (a, b) => a + b;
const sub: BinaryFn = (a, b) => a - b;
```

## [아이템13] 타입과 인터페이스의 차이점 알기

공통점

- 인터페이스와 타입 모두 타입별칭, 제네릭이 가능함
- 인터페이스는 타입을 확장할 수 있고 타입은 인터페이스를 확작할 수 있음

```ts
interface IStateWithPop extends TState {
  population: number;
}

type TStateWithPop = IState & { population: number };
```

- 클래스를 구현할 때는, 타입과 인터페이스 둘 다 사용할 수 있음

```ts
class StateT implements TState {
  name: string = "";
  capital: string = "";
}
```

차이점

- 인터페이스는 유니온 타입 같은 복잡한 타입을 확장하지는 못함 (복잡한 타입을 확장하고 싶으면 타입과 &를 사용해야함)
- 타입 키워드는 매핑된 타입 또는 조건부 타입 같은 고급 기능에 활용되기도 함
- 튜플과 배열 타입도 type 키워드를 이용해 간결하게 표현 할 수 있음

```ts
type Pair = [number, number];
type NamedNum = [string, ...number[]]; //const test: NamedNum = ["jiyoung", 1, 2];
```

- 인터페이스는 보강이 가능함
  선언 병합이 가능함 선언 병합이란?

```ts
interface State {
  name: string;
  capital: string;
}
interface State {
  population: number;
}
const wyoming: State {
  name: 'Wyoming',
  capital: 'Cheyenne',
  population: 500_00
};
```

- 프로퍼티가 추가되는 상황에서 유용함! 프로퍼티가 추가되는 것을 원하지 않는다면 인터페이스 대신 타입을 사용해야함

- 복잡한 타입이라면 타입 별칭을 사용
- 향후 선언 병합을 고려해야하는 상황이면 interface 사용
- 프로젝트 내부적으로 사용되는 타입에 선언 병합이 발생하는 것은 잘못된 설계.. (그러면 선언 병합은 버전 하위호환을 고려해야하는 상황에 사용해야하는건가)

## [아이템14] 타입 연산과 제네릭 사용으로 반복 줄이기

- 타입에서 중복이 흔한 이유는 공유된 패턴을 제거하는 메커니즘이 기존 코드에서 하던 것과 비교해 덜 익숙하기 때문
- 타입에 이름을 붙이면 타입 반복을 줄일 수 있음 => 코드에서 상수를 이용해 반복을 줄이는 것
- 공통된 부분을 분리, 다른 부분은 공통 타입을 확장
- 인터섹션 연산자로 타입을 확장하기도 함 (확장할 수 없는 유니온 타입에 유용)
- 타입에 대한 DRY 원칙의 핵심은 제네릭이다.
- 제네릭 타입에서 extends를 통해 제한된 제네릭을 받을 수 있다!

## [아이템15] 동적 데이터에 인덱스 시그니처 사용하기

- 런타임 때까지 객체의 속성을 알 수 없는 경우에민 인덱스 시그니처 사용
- 런타임에서 실제로 값이 일치한다는 보장이 없으면 값 타입에 undefined 추가
- Record를 사용해서 키 타입에 유연성을 제공할 수 있음

## [아이템16] number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기

-
