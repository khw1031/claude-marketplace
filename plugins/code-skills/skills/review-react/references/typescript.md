# default exports

프레임워크에서 명시적으로 요구하지 않는 한, default exports를 사용하지 마세요.

```ts
// BAD
export default function myFunction() {
  return <div>Hello</div>;
}
```

```ts
// GOOD
export function myFunction() {
  return <div>Hello</div>;
}
```

Default exports는 가져오는 파일에서 혼란을 야기합니다.

```ts
// BAD
import myFunction from "./myFunction";
```

```ts
// GOOD
import { myFunction } from "./myFunction";
```

프레임워크가 default export를 요구하는 특정 상황이 있습니다. 예를 들어, Next.js, Expo Router는 페이지 또는 스크린에 대해 default export를 요구합니다.

```tsx
// This is fine, if required by the framework
export default function MyPage() {
  return <div>Hello</div>;
}
```

# any inside generic functions

제네릭 함수를 빌드할 때, 함수 본문 내부에서 any를 사용해야 할 수도 있습니다.

이는 TypeScript가 종종 런타임 로직을 타입 수준 로직과 일치시키지 못하기 때문입니다.

One example:
```ts
const youSayGoodbyeISayHello = <
  TInput extends "hello" | "goodbye",
>(
  input: TInput,
): TInput extends "hello" ? "goodbye" : "hello" => {
  if (input === "goodbye") {
    return "hello"; // Error!
  } else {
    return "goodbye"; // Error!
  }
};
```

타입 레벨(그리고 런타임)에서, 이 함수는 입력이 `hello`일 때 `goodbye`를 반환합니다.
TypeScript에서 이를 간결하게 작동시킬 방법이 없습니다.
따라서 `any`를 사용하는 것이 가장 간결한 해결책입니다:

```ts
const youSayGoodbyeISayHello = <
  TInput extends "hello" | "goodbye",
>(
  input: TInput,
): TInput extends "hello" ? "goodbye" : "hello" => {
  if (input === "goodbye") {
    return "hello" as any;
  } else {
    return "goodbye" as any;
  }
};
```

제네릭 함수 외부에서는 `any`를 극히 드물게 사용하세요.

# discriminated unions

데이터가 여러 다른 형태 중 하나일 수 있는 경우를 모델링하기 위해 적극적으로 판별 유니온(discriminated unions)을 사용하세요.

예를 들어, 환경 간에 이벤트를 전송할 때:
```ts
type UserCreatedEvent = {
  type: "user.created";
  data: { id: string; email: string };
};

type UserDeletedEvent = {
  type: "user.deleted";
  data: { id: string };
};

type Event = UserCreatedEvent | UserDeletedEvent;
```

판별 유니온(discriminated unions)의 결과를 처리하기 위해 switch 문을 사용하세요:
```ts
const handleEvent = (event: Event) => {
  switch (event.type) {
    case "user.created":
      console.log(event.data.email);
      break;
    case "user.deleted":
      console.log(event.data.id);
      break;
  }
};
```

'선택적 속성들의 모음' 문제를 방지하기 위해 판별 유니온(discriminated unions)을 사용하세요.
예를 들어, 데이터 가져오기 상태를 설명할 때:

```ts
// BAD - allows impossible states
type FetchingState<TData> = {
  status: "idle" | "loading" | "success" | "error";
  data?: TData;
  error?: Error;
};

// GOOD - prevents impossible states
type FetchingState<TData> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: TData }
  | { status: "error"; error: Error };
```

# enums

코드베이스에 새로운 enum을 도입하지 마세요. 기존 enum은 유지하세요.
enum과 유사한 동작이 필요한 경우, `as const` 객체를 사용하세요:

```ts
const backendToFrontendEnum = {
  xs: "EXTRA_SMALL",
  sm: "SMALL",
  md: "MEDIUM",
} as const;

type LowerCaseEnum = keyof typeof backendToFrontendEnum; // "xs" | "sm" | "md"

type UpperCaseEnum =
  (typeof backendToFrontendEnum)[LowerCaseEnum]; // "EXTRA_SMALL" | "SMALL" | "MEDIUM"
```

숫자형 열거형(numeric enums)은 문자열 열거형(string enums)과 다르게 동작한다는 점을 기억하세요. 숫자형 열거형은 역방향 매핑(reverse mapping)을 생성합니다:
```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

const direction = Direction.Up; // 0
const directionName = Direction[0]; // "Up"
```

이는 위의 `Direction` enum이 네 개가 아닌 여덟 개의 키를 가지게 된다는 것을 의미합니다.

```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

Object.keys(Direction).length; // 8
```

# import type

타입을 가져올 때는 항상 import type을 사용하세요.
인라인 `import { type ... }` 보다 최상위 레벨 `import type`을 선호하세요.

```ts
// BAD
import { type User } from "./user";
```

```ts
// GOOD
import type { User } from "./user";
```

이러한 이유는 특정 환경에서는 첫 번째 버전의 import가 제거되지 않기 때문입니다. 따라서 다음과 같이 남게 됩니다:

```ts
// Before transpilation
import { type User } from "./user";

// After transpilation
import "./user";
```

# installing libraries

라이브러리를 설치할 때, 자신의 학습 데이터에 의존하지 마세요.

당신의 학습 데이터는 특정 시점에 끊겨 있습니다. 당신은 아마도 JavaScript와 TypeScript 세계의 최신 개발 사항을 모두 알지 못할 것입니다.

이는 수동으로 버전을 선택하는 대신(`package.json` 파일을 업데이트하는 방식으로), 라이브러리의 최신 버전을 설치하기 위해 스크립트를 사용해야 한다는 것을 의미합니다.

```bash
# pnpm
pnpm add -D @typescript-eslint/eslint-plugin

# yarn
yarn add -D @typescript-eslint/eslint-plugin

# npm
npm install --save-dev @typescript-eslint/eslint-plugin
```

이렇게 하면 항상 최신 버전을 사용할 수 있습니다.

# interface extends

상속을 모델링할 때는 항상 interface를 선호하세요.
TypeScript에서 `&` 연산자는 성능이 매우 좋지 않습니다. `interface extends`가 불가능한 경우에만 사용하세요.

```ts
// BAD

type A = {
  a: string;
};

type B = {
  b: string;
};

type C = A & B;
```

```ts
// GOOD

interface A {
  a: string;
}

interface B {
  b: string;
}

interface C extends A, B {
  // Additional properties can be added here
}
```

# jsdoc comments

함수와 타입을 주석 처리하기 위해 JSDoc 주석을 사용하세요.
JSDoc 주석에서는 간결하게 작성하고, 함수의 동작이 명확하지 않은 경우에만 JSDoc 주석을 제공하세요.
같은 파일 내의 다른 함수와 타입에 링크하려면 JSDoc 인라인 `@link` 태그를 사용하세요.

```ts
/**
 * Subtracts two numbers
 */
const subtract = (a: number, b: number) => a - b;

/**
 * Does the opposite to {@link subtract}
 */
const add = (a: number, b: number) => a + b;
```

# naming conventions

- 파일 이름에는 kebab-case를 사용하세요 (예: `my-component.ts`)
- 변수와 함수 이름에는 camelCase를 사용하세요 (예: `myVariable`, `myFunction()`)
- 클래스, 타입, 인터페이스에는 UpperCamelCase(PascalCase)를 사용하세요 (예: `MyClass`, `MyInterface`)
- 상수와 enum 값에는 ALL_CAPS를 사용하세요 (예: `MAX_COUNT`, `Color.RED`)
- 제네릭 타입, 함수 또는 클래스 내부에서는 타입 매개변수에 `T` 접두사를 붙이세요 (예: `TKey`, `TValue`)

```ts
type RecordOfArrays<TItem> = Record<string, TItem[]>;
```

# noUncheckedIndexedAccess

사용자가 `tsconfig.json`에서 이 규칙을 활성화한 경우, 객체와 배열의 인덱싱은 예상과 다르게 동작할 수 있습니다.

```ts
const obj: Record<string, string> = {};

// With noUncheckedIndexedAccess, value will
// be `string | undefined`
// Without it, value will be `string`
const value = obj.key;
```

```ts
const arr: string[] = [];

// With noUncheckedIndexedAccess, value will
// be `string | undefined`
// Without it, value will be `string`
const value = arr[0];
```

# optional properties

선택적 속성(optional properties)은 극히 드물게 사용하세요. 속성이 진정으로 선택적인 경우에만 사용하고, 속성을 전달하지 않아 발생할 수 있는 버그를 고려하세요.

아래 예시에서는 항상 사용자 ID를 `AuthOptions`에 전달하고자 합니다. 이는 코드베이스의 어딘가에서 이를 전달하는 것을 잊어버리면 함수가 인증되지 않는 상태가 될 수 있기 때문입니다.

```ts
// BAD
type AuthOptions = {
  userId?: string;
};

const func = (options: AuthOptions) => {
  const userId = options.userId;
};
```

```ts
// GOOD
type AuthOptions = {
  userId: string | undefined;
};

const func = (options: AuthOptions) => {
  const userId = options.userId;
};
```

# readonly properties

기본적으로 객체 타입에 `readonly` 속성을 사용하세요. 이는 런타임에 우발적인 변경을 방지합니다.
속성이 진정으로 변경 가능한 경우에만 `readonly`를 생략하세요.

```ts
// BAD
type User = {
  id: string;
};

const user: User = {
  id: "1",
};

user.id = "2";
```

```ts
// GOOD
type User = {
  readonly id: string;
};

const user: User = {
  id: "1",
};

user.id = "2"; // Error
```

# return types

모듈의 최상위 레벨에서 함수를 선언할 때, 반환 타입을 명시하세요. 이는 미래의 AI 어시스턴트가 함수의 목적을 이해하는 데 도움이 됩니다.

```ts
const myFunc = (): string => {
  return "hello";
};
```

이에 대한 한 가지 예외는 JSX를 반환하는 컴포넌트입니다. 컴포넌트의 반환 타입을 선언할 필요가 없습니다, 항상 JSX이기 때문입니다.

```tsx
const MyComponent = () => {
  return <div>Hello</div>;
};
```

# throwing

오류를 발생시키는 코드를 구현하기 전에 신중하게 생각하세요.

발생한 오류가 시스템에서 바람직한 결과를 생성한다면, 그렇게 하세요. 예를 들어, 백엔드 프레임워크의 요청 핸들러 내에서 사용자 정의 오류를 발생시키는 경우가 있습니다.

그러나 수동 try catch가 필요한 코드의 경우, 대신 결과 타입(result type)을 사용하는 것을 고려하세요:

```ts
type Result<T, E extends Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };
```

예를 들어, JSON을 파싱할 때:
```ts
const parseJson = (
  input: string,
): Result<unknown, Error> => {
  try {
    return { ok: true, value: JSON.parse(input) };
  } catch (error) {
    return { ok: false, error: error as Error };
  }
};
```

이런 방식으로 호출자에서 오류를 처리할 수 있습니다:
```ts
const result = parseJson('{"name": "John"}');

if (result.ok) {
  console.log(result.value);
} else {
  console.error(result.error);
}
```

# 타입 네이밍 규칙

타입 네이밍은 단수형을 사용해주세요.

```ts
// ❌ 잘못된 방식
type Routes = "user" | "admin/user" | "admin";

function goToRoute(route: Routes) { // route는 하나인데 Routes는 복수형
  // ...
}

// 올바른 방식
type Route = "user" | "admin/user" | "admin";

function goToRoute(route: Route) {
  // ...
}

// 배열이 필요한 경우
type RouteArray = Route[];
// 또는
const routes: Route[] = ["user", "admin"];
```

Union 타입도 실제로는 하나의 값만 나타내므로 단수형으로 네이밍하는 것이 올바름

변수와 타입의 케이스를 다르게 사용해주세요

```ts
// 권장 방식
type Route = "user" | "admin/user" | "admin";
const route: Route = "user"; // 변수는 camelCase, 타입은 PascalCase

// 작동하지만 비추천
type route = "user" | "admin/user" | "admin";
const route: route = "user"; // 문법 하이라이팅이 혼란스러워짐
```

문법 하이라이팅과 가독성 향상, TypeScript가 런타임과 타입 레벨을 구분할 수 있음

I, T 접두사로 interface/type 구분 금지

```ts
// ❌ 구식 Java 관례 - 비추천
interface IUser {
  name: string;
}

type TOrganization = {
  name: string;
}

// 권장 방식
interface User {
  name: string;
}

type Organization = {
  name: string;
}
```

- Java 관례의 잔재
- 호버로 interface/type 구분 가능
- 타입을 interface로 바꿀 때 이름도 변경해야 하는 번거로움