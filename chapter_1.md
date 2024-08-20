## [아이템1] 타입스크립트와 자바스크립트의 관계 이해하기

- 타입스크립트는 자바스크립트의 상위 집합(superset)
- 모든 자바스크립트 프로그램은 타입스크립트 참, 타입스크립트 프로그램이지만 자바스크립트가 아닌 프로그램이 존재함 => 타입스크립트가 타입을 명시하는 추가적인 문법을 가지기 때문
  **타입 시스템은 런타임에 오류를 발생시킬 코드를 미리 찾아냄**
- 타입 시스템은 자바스크립트의 런타임 동작을 '모델링'함
- 런타임 체크를 엄격하게 하는 언어에서 아래 코드는 런타임 오류가 발생하지만 타입 체커는 자바스크립트 런타임 동작을 모델링해 정상으로 인식함 (자바스크립트 런타임에서 23으로 정상 출력됨)

```js
const x = 2 + "3"; // 정상 string 타입입니다.
```

- 자바스크립트 런타임에서는 오류가 아니지만 추가적인 타입 체크를 하여 오류가 발생하는 경우도 있음

```js
const a = null + 7;
```

- 타입 체크를 통과해도 런타임 오류가 발생하는 경우도 있음
- 타입스크립트는 앞의 배열이 범위 내에서 사용될 것이라고 가정했지만 실제로는 범위를 벗어나서 런타임 오류가 발생
- 타입스크립트가 이해하는 값의 타입과 실제 값에 차이가 존재하기 때문에 발생하는 오류

```js
const name = ["hee", "jinu"];
console.log(name[2].toUpperCase());
```

## [아이템2] 타입스크립트 설정 이해하기

- 타입스크립트 컴파일러는 많은 설정을 가지고 있음
- 커맨드 라인보다 tsconfig.json 설정 파일을 사용하는 것이 좋음 => 협업하는 사람들이나 다른 도구들이 이 설정을 참고할 수 있음

**noImplicitAny**

- 변수들이 미리 정의된 타입을 가져야하는지 여부를 제어
- 새 프로젝트를 시작한다면 noImplicitAny를 설정하여 코드를 작성할때마다 타이블 명시하도록 해야함

**strictNullChecks**

- null과 undefined가 모든 타입에서 허용되는지 확인하는 설정

```js
// strictNullChecks를 해제하면 오류X
//설정하면 오류발생
const x: number = null;
```

- 만약 x 변수에 null을 허용하려면 명시적으로 타입을 작성

```js
const x: number | null = null;
```

- undefined 는 객체가 아닙니다. 와 같은 런타임 오류를 방지하기 위해서는 strictNullChecks 를 사용해야 한다.
- strict 설정을 하면 대부분 오류를 잡을 수 있음

## [아이템3] 코드 생성과 타입이 관계 없음을 이해하기

- 타입스크립트의 컴파일러는 브라우저에서 동작할 수 있도록 구버전의 자바스크립틀 트랜스파일하고 코드의 타입 오류를 체크함
- **코드 생성(컴파일)과 타입 시스템은 독립적**으로 동작하여 타입오류가 있는 코드도 컴파일이 가능함(오류가 있을 때 컴파일 하지 않으려면 noEmitOnError를 설정하면됨)

**타입은 런타임 동작이나 성능에 영향을 주지 않음**

- 자바스크립트로 컴파일 되는 과정에서 타입 관련 코드들 (인터페이스, 타입, 타입 구문)은 제거 됨

**런타임에서 타입을 지정하는 방법**

1. 속성 유무 체크로 타입 좁히기

```js
interface Dog {
  run: VoidFunction;
}

interface Bird {
  fly: VoidFunction;
}

function runAway(animal: Dog | Bird) {
  if ("run" in animal) {
    animal; // Dog
  }

  if ("fly" in animal) {
    animal; // Bird
  }
}
```

2. 태그된 유니온(tagged union)

- 런타임에 타입을 식별하기 위해 명시적 '태그'를 붙이는 것

```js
type ArticleA = {
  kind: "a",
  field1: string,
  field2: string,
  author: string,
};

type ArticleB = {
  kind: "b",
  field2: string,
  field3: string,
  author: string,
};

type ArticleC = {
  kind: "c",
  field3: string,
  field4: string,
  author: string,
};
```

```js
type Props = {
  content: ArticleA | ArticleB | ArticleC;
};

const Article: FC<Props> = ({ content }) => {
  if (content.kind === 'a') {
    content // ArticleA
  }

  if (content.kind === 'b') {
    content // ArticleB
  }

  return (...);
};
```

- kind 필드를 활용하면 각 타입의 필드 변화에 영향을 받지 않고 구분할 수 있음

3. 클래스

- 인터페이스는 타입으로만 사용 가능하지만, 클래스로 선언하면 타입과 값 모두 사용할 수 있음

---

- 타입스크립트에서는 런타임 타입과 선언된 타입이 맞지 않을 수 있음 선언된 타입이 언제든지 달라질 수 있다

- 타입스크립트 타입으로는 함수를 오버로딩(동일한 이름에 매개변수만 다른 여러버전의 함수를 허용하는 것) 할 수 없음
- 온전히 타입 수준에서만 오버로딩 할 수 있음 하나의 함수에 여러 개의 선언문을 작성할 수 있지만 구현체는 오직 하나
- 타입스크립트는 자바스크립트 변환 시점에 제거되어 런타임 성능에 영향을 주지 않음 => 런타임 오버헤드는 없지만 빌드타임 오버헤드는 존재

## [아이템4] 구조적 타이핑에 익숙해지기

- 덕 타이핑: 함수의 매개변수 값이 모두 제대로 주어진다면, 그 값이 어떻게 만들어졌는지 신경 쓰지 않고 사용한다.

```js
interface Vector2D {
  x: number;
  y: number;
}
function calculateLength(v: Vector2D) {
  return Math.sqrt(v.x * v.x + v.y * v.y);
}

interface NamedVector {
  name: string;
  x: number;
  y: number;
}
const v: NamedVector = { x: 3, y: 4, name: "zee" };
calculateLength(v); // 정상. 5
```

- NamedVector 가 Vector2D와 구조가 호환되기 때문에 calculateLength 호출이 가능 => 구조적 타이핑이 가능
- 구조적 타이핑 때문에 함수에서 없는값을 참조해 오류가 발생하기도 함
  ❓[구조적 타이핑과 덕타이핑의 차이가 뭘까?](https://vallista.kr/%EB%8D%95-%ED%83%80%EC%9D%B4%ED%95%91%EA%B3%BC-%EA%B5%AC%EC%A1%B0%EC%A0%81-%ED%83%80%EC%9D%B4%ED%95%91/)
- 함수에서 매개변수의 속성들이 매개변수 타입에 선언된 속성만 가지는 경우를 봉인된(sealed), 정확한(precise) 타입이라고 부른다. 열린 타입은 이와 반대 즉, 타입에 선언된 속성 외의 다른 속성들을 가지더라도 두 타입은 서로 호환
- 루프를 순회하는 것 보다는 모든 속성을 각각 더하는 구현이 더 낫다. 정확한 타입으로 객체를 순회하는 것은 어려움

**클래스와 관련된 할당문**

```js
class C {
  foo: string;
  constructor(foo: string) {
    this.foo = foo;
  }
}

const c = new C("instance of C");
const d: C = { foo: "객체 리터럴" }; //오류X
```

d 가 C 타입에 할당되는 당황스러운 결과가 발생 - d 는 string 타입의 foo 프로퍼티를 가진다. - 하나의 매개변수로 호출되는 생성자(Object.prototype)을 가진다.
c는 생성자로 class C 를 가진다.

- c의 생성자에 연산 로직이 존재한다면 d의 경우에는 생성자를 실행하지 않으므로 문제가 발생하게 됨

❓어떤 경우에 이런 상황이 발생하는지 더 구체적으로 생각해보고싶음

---

- 구조적 타이핑 덕분에 테스트를 작성할때는 내부 구현 추상화하여 유닛테스트에 용이함

## [아이템5] any 타입 지양하기

- 자동완성 기능과 적절한 도움말이 제공되지 않는다.
- 코드 리팩토링 때 버그를 감춘다.
- 런타임에 오류가 발생할 수 있게된다.
- any 타입을 사용하면 타입 설계가 불분명해져서 설계가 잘 되었는지 알 수가 없다. 설계가 명확히 보이도톡 타입을 일일이 작성하는게 좋다.
- 타입 시스템의 신뢰도를 떨어뜨린다.

## [아이템6] 편집기를 사용하여 타입 시스템 탐색하기

- 편집기에서 타입스크립트를 사용하는 방법에 대한 내용~~~

## [아이템7] 타입이 값들의 집합이라고 생각하기

- 타입이란?? 할당 가능한 값들의 집합
- 집합은 타입의 범위라고도 함
- never : 아무 값도 포함되지 않는 공집합 => 아무값도 할당할 수 없음
- 리터럴,유닛 : 한 가지 값만 포함하는 타입
- 유니온 타입 : 유닛 타입을 여러개로 묶은 것 합집합 `type AB = 'A' | 'B';`
- 인터섹션 : A타입과 B타입내의 속성을 모두 포함해야한다. (구조적 타이핑으로 인해 gender속성을 더 가져도 C타입에 속한다고 할 수 있음)

```js
interface A {
  name: string;
}
interface B {
  age: number;
}

type C = A & B;

const c: C = {
  name: "hee",
  age: 25,
};
```

```js
type K = keyof (A|B) // K는 never타입
```

❓ [유니온과 인터섹션](https://www.typescriptlang.org/play/?ssl=20&ssc=30&pln=1&pc=1#code/JYOwLgpgTgZghgYwgAgILIN4ChnJHAWwgC5kBnMKUAcwG4sBfLUSWRFAIUx2TmpLwBXAgCNo9JljABPAA4oAwsgC8aZADJkHelgQB7EBWQJSS1dlz4ipAEQALCBBsAaHnwEAmAKyvGOmfLIANIqyADWENJ6MMgAFKgAPhwAlFJyKNTUoYkcWLoGRtTEmaEY7sQAzM5WJADk1LVM+oZgyNQexGaY5VU1xLVQjUA)

- extends 키워드: 집합의 부분집합을 만듦

[아이템8] 타입 공간과 값 공간의 심벌 구분하기

- 타입스크립트의 심벌은 타입, 값 공간 둘 중 한곳에 존재한다.
- 타입은 컴파일 하는 과정에서 제거된다.

```
타입
type, interface
값
const, let, var
둘 다
class, 생성자함수, enum
```
