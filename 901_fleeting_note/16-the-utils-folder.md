[[_ts_index]]

---

this is Generic Functions

```typescript
const set = new Set<number>([1, 2, 3]);
//                 ^^^^^^^^ ^^^^^^^^^
//                 type     value
```

這個就沒有支援

```ts twoslash
// @errors: 2558
const obj = JSON.parse<{ hello: string }>('{"hello": "world"}');
```


### What Makes A Function Generic?

泛型fn 有 type paramater T

```typescript
function identity<T>(arg: T): T {
  //                 ^^^ Type parameter
  return arg;
}
```

or use arrow function syntax:

```typescript
const identity = <T>(arg: T): T => arg;
```

also can use it in a type
```typescript
type Identity = <T>(arg: T) => void;

const identity: Identity = (arg) => arg;

identity<number>(42);

```


type parameter的位置
一個在 fn 上
一個在 type 上
```typescript
// Type alias for a generic function
type Identity = <T>(arg: T) => void;
//              ^^^
//              Type parameter belongs to the function

// Generic type
type Identity<T> = (arg: T) => void;
//           ^^^
//           Type parameter belongs to the type
```


### What Happens When We Don't Pass In A Type Argument?

```ts twoslash


// generic type 一定要加上 type para
type StringArray = Array<string>

type AnyArray = Array
//              @errors: 2314


// generic fn 不一定需要, 會自動 infer
function identity<T>(arg: T): T {
  return arg;
}

const result = identity(42); // No error!


```

it can auto infer return a different type:
```ts twoslash
function identity<T>(arg: T): T {
  return arg;
}
// ---cut---
const result1 = identity("hello");
//    ^? "hello"

const result2 = identity({ hello: "world" });
//    ^? { hello: "world" }

const result3 = identity([1, 2, 3]);
//    ^? number[]



// 你也可以自己提供，那就會以這個為準
const result = identity<string>(42);
// Argument of type 'number' is not assignable to parameter of type 'string'.
```


### There Is No Such Thing As 'A Generic'

```typescript

// 這個 T 叫做 type paramter
const identity = <T>(arg: T) => arg;
```


```typescript

// 這個 number 是 type argument
const set = new Set<number>([1, 2, 3]);
```


- Type Parameter: The `<T>` in `identity<T>`.
- Type Argument: The `number` passed to `Set<number>`.
- Generic Class/Function/Type: A class, function or type that declares a type parameter.

When you break generics down into these terms, it becomes much easier to understand.

### The Problem Generic Functions Solve

- 共用同一個 fn
- 且有型別保護 (as long as the type we defined well)
```ts twoslash
const getFirstElement = <TMember>(arr: TMember[]) => {
  return arr[0];
};
// ---cut---
const firstNumber = getFirstElement([1, 2, 3]);
//    ^? number
const firstString = getFirstElement(["a", "b", "c"]);
//    ^? string
```


### Type Parameter Defaults

```typescript

// default type parameter
const createSet = <T = string>(arr?: T[]) => {
  return new Set(arr)
}


// no need to specify type argument
const defaultSet = createSet()
//    ^? Set<string>

// but you can specify type argument for other type
const numberSet = createSet<number>([1, 2, 3])
//    ^? Set<number>



```



如果你沒有設定 default, and TypeScript can't infer the type from the runtime arguments, it will default to `unknown`:

```ts twoslash
const createSet = <T>(arr?: T[]) => {
  return new Set(arr);
};

const unknownSet = createSet();
//    ^?
```


### Constraining Type Parameters


if you want to ensure the type have come prop, you can add constraints


Let's imagine a `removeId` function that takes an object and removes the `id` property:

```ts twoslash
// @errors: 2339
// Property 'id' does not exist on type 'unknown'.ts(2339)
const removeId = <TObj>(obj: TObj) => {
  const { id, ...rest } = obj;
  return rest;
};
```


To fix this, we can add a constraint to `TObj` that ensures it has an `id` property:
```typescript
const removeId = <TObj extends { id: unknown }>(obj: TObj) => {
  const { id, ...rest } = obj;
  return rest;
};
```

Now, when we use `removeId`, TypeScript will error if we don't pass in an object with an `id` property:

```ts


// @errors: 2353
const removeId = <TObj extends { id: unknown }>(obj: TObj) => {
  const { id, ...rest } = obj;
  return rest;
};

// Object literal may only specify known properties, and 'name' does not exist in type '{ id: unknown; }'.
const result = removeId({ name: "Alice" });
```



## Type Predicates

ts cannot correctly infer, sometimes, we need to use type predicates
```ts


type Album = {
  id: number
  title: string
  artist: string
  name: string
}

unction isAlbum(input: unknown) {
  return (
    typeof input === 'object' &&
    input !== null &&
    'id' in input &&
    'title' in input &&
    'artist' in input &&
    'name' in input &&
    typeof input.id === 'number' &&
    typeof input.title === 'string' &&
    typeof input.artist === 'string' &&
    typeof input.name === 'string'
  )
}
const run = (maybeAlbum: unknown) => {
  if (isAlbum(maybeAlbum)) {
    // error: 'maybeAlbum' is of type 'unknown'.
    maybeAlbum.name.toUpperCase()
  }
}


```

change to this can fix this err
```ts

function isAlbum(input: unknown): input is Album {
  return (
    typeof input === 'object' &&
    input !== null &&
    'id' in input &&
    'title' in input &&
    'artist' in input &&
    'name' in input &&
    typeof input.id === 'number' &&
    typeof input.title === 'string' &&
    typeof input.artist === 'string' &&
    typeof input.name === 'string'
  )
}
```


### Type Predicates Can be Unsafe
但是 treat type predicates as as `as` and `!`, 都是使用者自己cast的安全性，因此cast 錯了就不安全了


```typescript

// no error for below code
// even I remove a lot of check in isAblum

type Album = {
  id: number
  title: string
  artist: string
  name: string
}

function isAlbum(input: unknown): input is Album {
  return typeof input === 'object'
}
const run = (maybeAlbum: unknown) => {
  if (isAlbum(maybeAlbum)) {
    maybeAlbum.name.toUpperCase()
  }
}



```

In this case, any object passed to `isAlbum` will be considered an `Album`, even if it doesn't have the required properties. This is a common pitfall when working with type predicates - it's important to consider them about as unsafe as `as` and `!`.



## Assertion Functions


type predicate -> cast this is what type
assert function -> throw errr if this is not that type


```ts twoslash
type Album = {
  id: number;
  title: string;
  artist: string;
  year: number;
};

function assertIsAlbum(input: unknown): asserts input is Album {
  if (
    typeof input === "object" &&
    input !== null &&
    "id" in input &&
    "title" in input &&
    "artist" in input &&
    "year" in input
  ) {
    throw new Error("Not an Album!");
  }
}



function getAlbumTitle(item: unknown) {
  console.log(item);
  //          ^?

  assertIsAlbum(item); // 確認是我們要的

  console.log(item.title); // 這個時候 type 也可以正確推論
  //          ^?
}
```


### Assertion Functions Can Lie
跟 type predicate 一樣的問題

```typescript
type Album = {
  id: number
  title: string
  artist: string
  year: number
}

function assertIsAlbum(input: unknown): asserts input is Album {
  if (typeof input === 'object') {
    throw new Error('Not an Album!')
  }
}

let item = null

// here will have run-time error since we misuse type assertion
assertIsAlbum(item)

```



# 看到這裡 
## Function Overloads

Function overloads provide a way to define multiple function signatures for a single function implementation. In other words, you can define different ways to call a function, each with its own set of parameters and return types. It's an interesting technique for creating a flexible API that can handle different use cases while maintaining type safety.

To demonstrate how function overloads work, we'll create a `searchMusic` function that allows for different ways to perform a search based on the provided arguments.

### Defining Overloads

To define function overloads, the same function definition is written multiple times with different parameter and return types. Each definition is called an overload signature, and is separated by semicolons. You'll also need to use the `function` keyword each time.

For the `searchMusic` example, we want to allow users to search by providing an artist, genre and year. But for legacy reasons, we want them to be able to pass them as a single object or as separate arguments.

Here's how we could define these function overload signatures. The first signature takes in three separate arguments, while the second signature takes in a single object with the properties:

```ts twoslash
// @errors: 2391
function searchMusic(artist: string, genre: string, year: number): void;
function searchMusic(criteria: {
  artist: string;
  genre: string;
  year: number;
}): void;
```

But we're getting an error. We've declared some ways this function should be declared, but we haven't provided the implementation yet.

### The Implementation Signature

The implementation signature is the actual function declaration that contains the actual logic for the function. It is written below the overload signatures, and must be compatible with all the defined overloads.

In this case, the implementation signature will take in a parameter called `queryOrCriteria` that can be either a `string` or an object with the specified properties. Inside the function, we'll check the type of `queryOrCriteria` and perform the appropriate search logic based on the provided arguments:

```typescript
function searchMusic(artist: string, genre: string, year: number): void;
function searchMusic(criteria: {
  artist: string;
  genre: string;
  year: number;
}): void;
function searchMusic(
  artistOrCriteria: string | { artist: string; genre: string; year: number },
  genre?: string,
  year?: number,
): void {
  if (typeof artistOrCriteria === "string") {
    // Search with separate arguments
    search(artistOrCriteria, genre, year);
  } else {
    // Search with object
    search(
      artistOrCriteria.artist,
      artistOrCriteria.genre,
      artistOrCriteria.year,
    );
  }
}
```

Now we can call the `searchMusic` function with the different arguments defined in the overloads:

```typescript
searchMusic("King Gizzard and the Lizard Wizard", "Psychedelic Rock", 2021);
searchMusic({
  artist: "Tame Impala",
  genre: "Psychedelic Rock",
  year: 2015,
});
```

However, TypeScript will warn us if we attempt to pass in an argument that doesn't match any of the defined overloads:

```ts twoslash
// @errors: 2575
function searchMusic(artist: string, genre: string, year: number): void;
function searchMusic(criteria: {
  artist: string;
  genre: string;
  year: number;
}): void;
function searchMusic(
  artistOrCriteria: string | { artist: string; genre: string; year: number },
  genre?: string,
  year?: number,
): void {}
// ---cut---
searchMusic(
  {
    artist: "Tame Impala",
    genre: "Psychedelic Rock",
    year: 2015,
  },
  "Psychedelic Rock",
);
```

This error shows us that we're trying to call `searchMusic` with two arguments, but the overloads only expect one or three arguments.

### Function Overloads vs Unions

Function overloads can be useful when you have multiple call signatures spread over different sets of arguments. In the example above, we can either call the function with one argument, or three.

When you have the same number of arguments but different types, you should use a union type instead of function overloads. For example, if you want to allow the user to search by either artist name or criteria object, you could use a union type:

```typescript
function searchMusic(
  query: string | { artist: string; genre: string; year: number },
): void {
  if (typeof query === "string") {
    // Search by artist
    searchByArtist(query);
  } else {
    // Search by all
    search(query.artist, query.genre, query.year);
  }
}
```

This uses far fewer lines of code than defining two overloads and an implementation.

## Exercises

### Exercise 1: Make a Function Generic

Here we have a function `createStringMap`. The purpose of this function is to generate a `Map` with keys as strings and values of the type passed in as arguments:

```typescript
const createStringMap = () => {
  return new Map();
};
```

As it currently stands, we get back a `Map<any, any>`. However, the goal is to make this function generic so that we can pass in a type argument to define the type of the values in the `Map`.

For example, if we pass in `number` as the type argument, the function should return a `Map` with values of type `number`:

```ts twoslash
// @errors: 2558 2578
const createStringMap = () => {
  return new Map();
};
// ---cut---
const numberMap = createStringMap<number>();

numberMap.set("foo", 123);
```

Likewise, if we pass in an object type, the function should return a `Map` with values of that type:

```ts twoslash
// @errors: 2558 2578
const createStringMap = () => {
  return new Map();
};
// ---cut---
const objMap = createStringMap<{ a: number }>();

objMap.set("foo", { a: 123 });

objMap.set(
  "bar",
  // @ts-expect-error
  { b: 123 },
);
```

The function should also default to `unknown` if no type is provided:

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";
const createStringMap = () => {
  return new Map();
};
// ---cut---
const unknownMap = createStringMap();

type test = Expect<Equal<typeof unknownMap, Map<string, unknown>>>;
```

Your task is to transform `createStringMap` into a generic function capable of accepting a type argument to describe the values of Map. Make sure it functions as expected for the provided test cases.

<Exercise title="Exercise 1: Make a Function Generic" filePath="/src/085-the-utils-folder/215-generic-functions-without-inference.problem.ts"></Exercise>

### Exercise 2: Default Type Arguments

After making the `createStringMap` function generic in Exercise 1, calling it without a type argument defaults to values being typed as `unknown`:

```typescript
const stringMap = createStringMap();

// hovering over stringMap shows:
const stringMap: Map<string, unknown>;
```

Your goal is to add a default type argument to the `createStringMap` function so that it defaults to `string` if no type argument is provided. Note that you will still be able to override the default type by providing a type argument when calling the function.

<Exercise title="Exercise 2: Default Type Arguments" filePath="/src/085-the-utils-folder/216-type-parameter-defaults-in-generic-functions.problem.ts"></Exercise>

### Exercise 3: Inference in Generic Functions

Consider this `uniqueArray` function:

```typescript
const uniqueArray = (arr: any[]) => {
  return Array.from(new Set(arr));
};
```

The function accepts an array as an argument, then converts it to a `Set`, then returns it as a new array. This is a common pattern for when you want to have unique values inside your array.

While this function operates effectively at runtime, it lacks type safety. It transforms any array passed in into `any[]`.

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";
import { it, expect } from "vitest";
const uniqueArray = (arr: any[]) => {
  return Array.from(new Set(arr));
};
// ---cut---
it("returns an array of unique values", () => {
  const result = uniqueArray([1, 1, 2, 3, 4, 4, 5]);

  type test = Expect<Equal<typeof result, number[]>>;

  expect(result).toEqual([1, 2, 3, 4, 5]);
});

it("should work on strings", () => {
  const result = uniqueArray(["a", "b", "b", "c", "c", "c"]);

  type test = Expect<Equal<typeof result, string[]>>;

  expect(result).toEqual(["a", "b", "c"]);
});
```

Your task is to boost the type safety of the `uniqueArray` function by making it generic.

Note that in the tests, we do not explicitly provide type arguments when invoking the function. TypeScript should be able to infer the type from the argument.

Adjust the function and insert the necessary type annotations to ensure that the `result` type in both tests is inferred as `number[]` and `string[]`, respectively.

<Exercise title="Exercise 3: Inference in Generic Functions" filePath="/src/085-the-utils-folder/217-generic-functions-with-inference.problem.ts"></Exercise>

### Exercise 4: Type Parameter Constraints

Consider this function `addCodeToError`, which accepts a type parameter `TError` and returns an object with a `code` property:

```ts twoslash
// @errors: 2339
const UNKNOWN_CODE = 8000;

const addCodeToError = <TError>(error: TError) => {
  return {
    ...error,
    code: error.code ?? UNKNOWN_CODE,
  };
};
```

If the incoming error doesn't include a `code`, the function assigns a default `UNKNOWN_CODE`. Currently there is an error under the `code` property.

Currently, there are no constraints on `TError`, which can be of any type. This leads to errors in our tests:

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";
import { it, expect } from "vitest";

const UNKNOWN_CODE = 8000;

const addCodeToError = <TError>(error: TError) => {
  return {
    ...error,
    code: (error as any).code ?? UNKNOWN_CODE,
  };
};
// ---cut---
it("Should accept a standard error", () => {
  const errorWithCode = addCodeToError(new Error("Oh dear!"));

  type test1 = Expect<Equal<typeof errorWithCode, Error & { code: number }>>;

  console.log(errorWithCode.message);

  type test2 = Expect<Equal<typeof errorWithCode.message, string>>;
});

it("Should accept a custom error", () => {
  const customErrorWithCode = addCodeToError({
    message: "Oh no!",
    code: 123,
    filepath: "/",
  });

  type test3 = Expect<
    Equal<
      typeof customErrorWithCode,
      {
        message: string;
        code: number;
        filepath: string;
      } & {
        code: number;
      }
    >
  >;

  type test4 = Expect<Equal<typeof customErrorWithCode.message, string>>;
});
```

Your task is to update the `addCodeToError` type signature to enforce the required constraints so that `TError` is required to have a `message` property and can optionally have a `code` property.

<Exercise title="Exercise 4: Type Parameter Constraints" filePath="/src/085-the-utils-folder/216-type-parameter-defaults-in-generic-functions.problem.ts"></Exercise>

### Exercise 5: Combining Generic Types and Functions

Here we have `safeFunction`, which accepts a function `func` typed as `PromiseFunc` that returns a function itself. However, if `func` encounters an error, it is caught and returned instead:

```typescript
type PromiseFunc = () => Promise<any>;

const safeFunction = (func: PromiseFunc) => async () => {
  try {
    const result = await func();
    return result;
  } catch (e) {
    if (e instanceof Error) {
      return e;
    }
    throw e;
  }
};
```

In short, the thing that we get back from `safeFunction` should either be the thing that's returned from `func` or an `Error`.

However, there are some issues with the current type definitions.

The `PromiseFunc` type is currently set to always return `Promise<any>`. This means that the function returned by `safeFunction` is supposed to return either the result of `func` or an `Error`, but at the moment, it's just returning `Promise<any>`.

There are several tests that are failing due to these issues:

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";
import { it, expect } from "vitest";

type PromiseFunc = () => Promise<any>;

const safeFunction = (func: PromiseFunc) => async () => {
  try {
    const result = await func();
    return result;
  } catch (e) {
    if (e instanceof Error) {
      return e;
    }
    throw e;
  }
};

// ---cut---
it("should return an error if the function throws", async () => {
  const func = safeFunction(async () => {
    if (Math.random() > 0.5) {
      throw new Error("Something went wrong");
    }
    return 123;
  });

  type test1 = Expect<Equal<typeof func, () => Promise<Error | number>>>;

  const result = await func();

  type test2 = Expect<Equal<typeof result, Error | number>>;
});

it("should return the result if the function succeeds", async () => {
  const func = safeFunction(() => {
    return Promise.resolve(`Hello!`);
  });

  type test1 = Expect<Equal<typeof func, () => Promise<string | Error>>>;

  const result = await func();

  type test2 = Expect<Equal<typeof result, string | Error>>;

  expect(result).toEqual("Hello!");
});
```

Your task is to update `safeFunction` to have a generic type parameter, and update `PromiseFunc` to not return `Promise<Any>`. This will require you to combine generic types and functions to ensure that the tests pass successfully.

<Exercise title="Exercise 5: Combining Generic Types and Functions" filePath="/src/085-the-utils-folder/219-combining-generic-types-with-generic-functions.problem.ts"></Exercise>

### Exercise 6: Multiple Type Arguments in a Generic Function

After making the `safeFunction` generic in Exercise 5, it's been updated to allow for passing arguments:

```typescript
const safeFunction =
  <TResult>(func: PromiseFunc<TResult>) =>
  async (...args: any[]) => {
    //   ^^^^^^^^^^^^^^ Now can receive args!
    try {
      const result = await func(...args);
      return result;
    } catch (e) {
      if (e instanceof Error) {
        return e;
      }
      throw e;
    }
  };
```

Now that the function being passed into `safeFunction` can receive arguments, the function we get back should _also_ contain those arguments and require you to pass them in.

However, as seen in the tests, this isn't working:

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";
import { it, expect } from "vitest";

type PromiseFunc<T> = (...args: any[]) => Promise<T>;

const safeFunction =
  <TResult>(func: PromiseFunc<TResult>) =>
  async (...args: any[]) => {
    //   ^^^^^^^^^^^^^^ Now can receive args!
    try {
      const result = await func(...args);
      return result;
    } catch (e) {
      if (e instanceof Error) {
        return e;
      }
      throw e;
    }
  };
// ---cut---
it("should return the result if the function succeeds", async () => {
  const func = safeFunction((name: string) => {
    return Promise.resolve(`hello ${name}`);
  });

  type test1 = Expect<
    Equal<typeof func, (name: string) => Promise<Error | string>>
  >;
});
```

For example, in the above test the `name` isn't being inferred as a parameter of the function returned by `safeFunction`. Instead, it's actually saying that we can pass in as many arguments as we want to into the function, which isn't correct.

```typescript
// hovering over func shows:
const func: (...args: any[]) => Promise<string | Error>;
```

Your task is to add a second type parameter to `PromiseFunc` and `safeFunction` to infer the argument types accurately.

As seen in the tests, there are cases where no parameters are necessary, and others where a single parameter is needed:

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";
import { it, expect } from "vitest";

type PromiseFunc<T> = (...args: any[]) => Promise<T>;

const safeFunction =
  <TResult>(func: PromiseFunc<TResult>) =>
  async (...args: any[]) => {
    try {
      const result = await func(...args);
      return result;
    } catch (e) {
      if (e instanceof Error) {
        return e;
      }
      throw e;
    }
  };
// ---cut---
it("should return an error if the function throws", async () => {
  const func = safeFunction(async () => {
    if (Math.random() > 0.5) {
      throw new Error("Something went wrong");
    }
    return 123;
  });

  type test1 = Expect<Equal<typeof func, () => Promise<Error | number>>>;

  const result = await func();

  type test2 = Expect<Equal<typeof result, Error | number>>;
});

it("should return the result if the function succeeds", async () => {
  const func = safeFunction((name: string) => {
    return Promise.resolve(`hello ${name}`);
  });

  type test1 = Expect<
    Equal<typeof func, (name: string) => Promise<Error | string>>
  >;

  const result = await func("world");

  type test2 = Expect<Equal<typeof result, string | Error>>;

  expect(result).toEqual("hello world");
});
```

Update the types of the function and the generic type, and make these tests pass successfully.

<Exercise title="Exercise 6: Multiple Type Arguments in a Generic Function" filePath="/src/085-the-utils-folder/220-multiple-type-arguments-in-generic-functions.problem.ts"></Exercise>

### Exercise 8: Assertion Functions

This exercise starts with an interface `User`, which has properties `id` and `name`. Then we have an interface `AdminUser`, which extends `User`, inheriting all its properties and adding a `roles` string array property:

```typescript
interface User {
  id: string;
  name: string;
}

interface AdminUser extends User {
  roles: string[];
}
```

The function `assertIsAdminUser` accepts either a `User` or `AdminUser` object as an argument. If the `roles` property isn't present in the argument, the function throws an error:

```typescript
function assertIsAdminUser(user: User | AdminUser) {
  if (!("roles" in user)) {
    throw new Error("User is not an admin");
  }
}
```

This function's purpose is to verify we are able to access properties that are specific to the `AdminUser`, such as `roles`.

In the `handleRequest` function, we call `assertIsAdminUser` and expect the type of `user` to be narrowed down to `AdminUser`.

But as seen in this test case, it doesn't work as expected:

```ts twoslash
// @errors: 2344 2339
import { Equal, Expect } from "@total-typescript/helpers";

interface User {
  id: string;
  name: string;
}

interface AdminUser extends User {
  roles: string[];
}

function assertIsAdminUser(user: User | AdminUser) {
  if (!("roles" in user)) {
    throw new Error("User is not an admin");
  }
}

// ---cut---
const handleRequest = (user: User | AdminUser) => {
  type test1 = Expect<Equal<typeof user, User | AdminUser>>;

  assertIsAdminUser(user);

  type test2 = Expect<Equal<typeof user, AdminUser>>;

  user.roles;
};
```

The `user` type is `User | AdminUser` before `assertIsAdminUser` is called, but it doesn't get narrowed down to just `AdminUser` after the function is called. This means we can't access the `roles` property.

Your task is to update the `assertIsAdminUser` function with the proper type assertion so that the `user` is identified as an `AdminUser` after the function is called.

<Exercise title="Exercise 8: Assertion Functions" filePath="/src/085-the-utils-folder/222-assertion-functions.problem.ts"></Exercise>

### Solution 1: Make a Function Generic

The first thing we'll do to make this function generic is to add a type parameter `T`:

```typescript
const createStringMap = <T>() => {
  return new Map();
};
```

With this change, our `createStringMap` function can now handle a type argument `T`.

The error has disappeared from the `numberMap` variable, but the function is still returning a `Map<any, any>`:

```typescript
const numberMap = createStringMap<number>();

// hovering over createStringMap shows:
const createStringMap: <number>() => Map<any, any>;
```

We need to specify the types for the map entries.

Since we know that the keys will always be strings, we'll set the first type argument of `Map` to `string`. For the values, we'll use our type parameter `T`:

```typescript
const createStringMap = <T>() => {
  return new Map<string, T>();
};
```

Now the function can correctly type the map's values.

If we don't pass in a type argument, the function will default to `unknown`:

```typescript
const objMap = createStringMap();

// hovering over objMap shows:
const objMap: Map<string, unknown>;
```

Through these steps, we've successfully transformed `createStringMap` from a regular function into a generic function capable of receiving type arguments .

### Solution 2: Default Type Arguments

The syntax for setting default types for generic functions is the same as for generic types:

```typescript
const createStringMap = <T = string>() => {
  return new Map<string, T>();
};
```

By using the `T = string` syntax, we tell the function that if no type argument is supplied, it should default to `string`.

Now when we call `createStringMap()` without a type argument, we end up with a `Map` where both keys and values are `string`:

```typescript
const stringMap = createStringMap();

// hovering over stringMap shows:
const stringMap: Map<string, string>;
```

If we attempt to assign a number as a value, TypeScript gives us an error because it expects a string:

```ts twoslash
// @errors: 2345
const createStringMap = <T = string>() => {
  return new Map<string, T>();
};

const stringMap = createStringMap();

// ---cut---
stringMap.set("bar", 123);
```

However, we can still override the default type by providing a type argument when calling the function:

```typescript
const numberMap = createStringMap<number>();
numberMap.set("foo", 123);
```

In the above code, `numberMap` will result in a `Map` with `string` keys and `number` values, and TypeScript will give an error if we try assigning a non-number value:

```typescript
numberMap.set(
  "bar",
  // @ts-expect-error
  true,
);
```

### Solution 3: Inference in Generic Functions

The first step is to add a type parameter onto `uniqueArray`. This turns `uniqueArray` into a generic function that can receive type arguments:

```typescript
const uniqueArray = <T>(arr: any[]) => {
  return Array.from(new Set(arr));
};
```

Now when we hover over a call to `uniqueArray`, we can see that it is inferring the type as `unknown`:

```ts twoslash
const uniqueArray = <T>(arr: any[]) => {
  return Array.from(new Set(arr));
};

// ---cut---
const result = uniqueArray([1, 1, 2, 3, 4, 4, 5]);
//             ^?
```

This is because we haven't passed any type arguments to it. If there's no type argument and no default, it defaults to unknown.

We want the type argument to be inferred as a number because we know that the thing we're getting back is an array of numbers.

So what we'll do is add a return type of `T[]` to the function:

```typescript
const uniqueArray = <T>(arr: any[]): T[] => {
  return Array.from(new Set(arr));
};
```

Now the result of `uniqueArray` is inferred as an `unknown` array:

```ts twoslash
const uniqueArray = <T>(arr: any[]): T[] => {
  return Array.from(new Set(arr));
};

// ---cut---
const result = uniqueArray([1, 1, 2, 3, 4, 4, 5]);
//    ^?
```

Again, the reason for this is that we haven't passed any type arguments to it. If there's no type argument and no default, it defaults to unknown.

If we add a `<number>` type argument to the call, the `result` will now be inferred as a number array:

```ts twoslash
const uniqueArray = <T>(arr: any[]): T[] => {
  return Array.from(new Set(arr));
};
// ---cut---
const result = uniqueArray<number>([1, 1, 2, 3, 4, 4, 5]);
//       ^?
```

However, at this point there's no relationship between the things we're passing in and the thing we're getting out. Adding a type argument to the call returns an array of that type, but the `arr` parameter in the function itself is still typed as `any[]`.

What we need to do is tell TypeScript that the type of the `arr` parameter is the same type as what is passed in.

To do this, we'll replace `arr: any[]` with `arr: T[]`:

```typescript
const uniqueArray = <T>(arr: T[]): T[] => {
  ...
```

The function's return type is an array of `T`, where `T` represents the type of elements in the array supplied to the function.

Thus, TypeScript can infer the return type as `number[]` for an input array of numbers, or `string[]` for an input array of strings, even without explicit return type annotations. As we can see, the tests pass successfully:

```typescript
// number test
const result = uniqueArray([1, 1, 2, 3, 4, 4, 5]);

type test = Expect<Equal<typeof result, number[]>>;

// string test
const result = uniqueArray(["a", "b", "b", "c", "c", "c"]);

type test = Expect<Equal<typeof result, string[]>>;
```

If you explicitly pass a type argument, TypeScript will use it. If you don't, TypeScript attempts to infer it from the runtime arguments.

### Solution 4: Type Parameter Constraints

The syntax to add constraints is the same as what we saw for generic types.

We need to use the `extends` keyword to add constraints to the generic type parameter `TError`. The object passed in is required to have a `message` property of type `string`, and can optionally have a `code` of type `number`:

```typescript
const UNKNOWN_CODE = 8000;

const addCodeToError = <TError extends { message: string; code?: number }>(
  error: TError,
) => {
  return {
    ...error,
    code: error.code ?? UNKNOWN_CODE,
  };
};
```

This change ensures that `addCodeToError` must be called with an object that includes a `message` string property. TypeScript also knows that `code` could either be a number or `undefined`. If `code` is absent, it will default to `UNKNOWN_CODE`.

These constraints make our tests pass, including the case where we pass in an extra `filepath` property. This is because using `extends` in generics does not restrict you to only passing in the properties defined in the constraint.

### Solution 5: Combining Generic Types and Functions

Here's the starting point of our `safeFunction`:

```typescript
type PromiseFunc = () => Promise<any>;

const safeFunction = (func: PromiseFunc) => async () => {
  try {
    const result = await func();
    return result;
  } catch (e) {
    if (e instanceof Error) {
      return e;
    }
    throw e;
  }
};
```

The first thing we'll do is update the `PromiseFunc` type to be a generic type. We'll call the type parameter `TResult` to represent the type of the value returned by the promise, and and it to the return type of the function:

```typescript
type PromiseFunc<TResult> = () => Promise<TResult>;
```

With this update, we now need to update the `PromiseFunc` in the `safeFunction` to include the type argument:

```typescript
const safeFunction =
  <TResult>(func: PromiseFunc<TResult>) =>
  async () => {
    try {
      const result = await func();
      return result;
    } catch (e) {
      if (e instanceof Error) {
        return e;
      }
      throw e;
    }
  };
```

With these changes in place, when we hover over the `safeFunction` call in the first test, we can see that the type argument is inferred as `number` as expected:

```typescript
it("should return an error if the function throws", async () => {
  const func = safeFunction(async () => {
    if (Math.random() > 0.5) {
      throw new Error("Something went wrong");
    }
    return 123;
  });
  ...

// hovering over safeFunction shows:
const safeFunction: <number>(func: PromiseFunc<number>) => Promise<() => Promise<number | Error>>
```

The other tests pass as well.

Whatever we pass into `safeFunction` will be inferred as the type argument for `PromiseFunc`. This is because the type argument is being inferred _inside_ the generic function.

This combination of generic functions and generic types can make your generic functions a lot easier to read.

### Solution 6: Multiple Type Arguments in a Generic Function

Here's how `PromiseFunc` is currently defined:

```typescript
type PromiseFunc<TResult> = (...args: any[]) => Promise<TResult>;
```

The first thing to do is figure out the types of the arguments being passed in. Currently, they're set to one value, but they need to be different based on the type of function being passed in.

Instead of having `args` be of type `any[]`, we want to spread in all of the `args` and capture the entire array.

To do this, we'll update the type to be `TArgs`. Since `args` needs to be an array, we'll say that `TArgs extends any[]`. Note that this doesn't mean that `TArgs` will be typed as `any`, but rather that it will accept any kind of array:

```typescript
type PromiseFunc<TArgs extends any[], TResult> = (
  ...args: TArgs
) => Promise<TResult>;
```

You might have tried this with `unknown[]` - but `any[]` is the only thing that works in this scenario.

Now we need to update the `safeFunction` so that it has the same arguments as `PromiseFunc`. To do this, we'll add `TArgs` to its type parameters.

Note that we also need to update the args for the `async` function to be of type `TArgs`:

```typescript
const safeFunction =
  <TArgs extends any[], TResult>(func: PromiseFunc<TArgs, TResult>) =>
  async (...args: TArgs) => {
    try {
      const result = await func(...args);
      return result;
    } catch (e) {
      ...
```

This change is necessary in order to make sure the function returned by `safeFunction` has the same typed arguments as the original function.

With these changes, all of our tests pass as expected.

### Solution 8: Assertion Functions

The solution is to add a type annotation onto the return type of `assertIsAdminUser`.

If it was a type predicate, we would say `user is AdminUser`:

```ts twoslash
// @errors: 2355
type User = {
  id: string;
  name: string;
};
type AdminUser = {
  id: string;
  name: string;
  roles: string[];
};

// ---cut---
function assertIsAdminUser(user: User): user is AdminUser {
  if (!("roles" in user)) {
    throw new Error("User is not an admin");
  }
}
```

However, this leads to an error. We get this error because `assertIsAdminUser` is returning `void`, which is different from a type predicate that requires you to return a Boolean.

Instead, we need to add the `asserts` keyword to the return type:

```typescript
function assertIsAdminUser(user: User | AdminUser): asserts user is AdminUser {
  if (!("roles" in user)) {
    throw new Error("User is not an admin");
  }
}
```

By adding the `asserts` keyword, just by the fact that `assertIsAdminUser` is called we can assert that the user is an `AdminUser`. We don't need to put it inside an `if` statement or anywhere else.

With the `asserts` change in place, the `user` type is narrowed down to `AdminUser` after `assertIsAdminUser` is called and the test passes as expected:

```typescript
const handleRequest = (user: User | AdminUser) => {
  type test1 = Expect<Equal<typeof user, User | AdminUser>>;

  assertIsAdminUser(user);

  type test2 = Expect<Equal<typeof user, AdminUser>>;

  user.roles;
};

// hovering over roles shows:
user: AdminUser;
```

