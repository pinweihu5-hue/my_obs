[[_ts_index]]


---


## Generic Types

**type function**? 
**These types take in a type and a key, and return a new type**

like `Pick` and `Omit`. 

hint: 實作用的就是泛型
```tsx


type Ex1 = { a: string; b: number }
type Ex2 = Pick<Ex1, "a">;
```


用 泛型來共用下面這兩個8成都一樣的 type
不同的地方是 `content`
```tsx
type StreamingPlaylist =
  | {
      status: "available";
      content: {  // playlist have content with name
        id: number;
        name: string;  
        tracks: string[];
      };
    }
  | {
      status: "unavailable";
      reason: string;
    };

type StreamingAlbum =
  | {
      status: "available";
      content: {  // Album content have title and artist
        id: number;
        title: string;  
        artist: string; 
        tracks: string[];
      };
    }
  | {
      status: "unavailable";
      reason: string;
    };
```


 we use a _type parameter_( ex `<TContent>`) that declares what type of arguments


result:
```ts


type ResourceStatus<TContent> =
  | {
      status: "available";
      content: TContent;
    }
  | {
      status: "unavailable";
      reason: string;
    };



type PlaylistContent = {
  id: number;
  name: string;
  tracks: string[];
};

type AlbumContent = {
  id: number;
  title: string;
  artist: string;
  tracks: string[];
};

type StreamingPlaylist = ResourceStatus<PlaylistContent>;
type StreamingAlbum = ResourceStatus<AlbumContent>;




```




### Multiple Type Parameters

Generic types can accept multiple type parameters

adding `TMetadata`

```tsx
type ResourceStatus<TContent, TMetadata> =
  | {
      status: "available";
      content: TContent;
      metadata: TMetadata;
    }
  | {
      status: "unavailable";
      reason: string;
    };
```

Now we can define the `StreamingPlaylist` and `StreamingAlbum` types, we can include metadata specific to each resource:

```tsx
type StreamingPlaylist = ResourceStatus<
  {
    id: number;
    name: string;
    tracks: string[];
  },
  {
    creator: string;
    artwork: string;
    dateUpdated: Date;
  }
>;

type StreamingAlbum = ResourceStatus<
  {
    id: number;
    title: string;
    artist: string;
    tracks: string[];
  },
  {
    recordLabel: string;
    upc: string;
    yearOfRelease: number;
  }
>;
```

Like before, each type maintains the same structure defined in `ResourceStatus`, but with its own content and metadata.



### All Type Arguments Must Be Provided
 
 by default, all generic types _require_ their type arguments to be passed in, just like runtime functions.

```ts twoslash
// @errors: 2314
type ResourceStatus<TContent, TMetadata> =
  | {
      status: "available";
      content: TContent;
      metadata: TMetadata;
    }
  | {
      status: "unavailable";
      reason: string;
    };
// ---cut---
type Example = ResourceStatus;
```

TypeScript shows an error that tells us that `ResourceStatus` requires two type arguments. This is because

### Default Type Parameters

In some cases, you may want to provide default types for generic type parameters. **Like with functions, you can use the = to assign a default value.**

By setting TMetadata's default value to an empty, we can essentially make TMetadata optional:

```tsx
type ResourceStatus<TContent, TMetadata = {}> =
  | {
      status: "available";
      content: TContent;
      metadata: TMetadata;
    }
  | {
      status: "unavailable";
      reason: string;
    };
```

Now, we can create a `StreamingPlaylist` type without providing a `TMetadata` type argument:

```tsx
type StreamingPlaylist = ResourceStatus<{
  id: number;
  name: string;
  tracks: string[];
}>;
```

If we hover over it, we'll see that it's typed as expected, with `metadata` being an empty object:

```tsx
type StreamingPlaylist =
  | {
      status: "unavailable";
      reason: string;
    }
  | {
      status: "available";
      content: {
        id: number;
        name: string;
        tracks: string[];
      };
      metadata: {};
    };
```

Defaults can help make your generic types more flexible and easier to use.

### Type Parameter Constraints

To set constraints on type parameters, we can use the `extends` keyword.

We can force the `TMetadata` type parameter to be an object while still defaulting to an empty object:

```tsx
type ResourceStatus<TContent, TMetadata extends object = {}> = // ...
```

There's also an opportunity to provide a constraint for the `TContent` type parameter.

Both of the `StreamingPlaylist` and `StreamingAlbum` types have an `id` property in their `content` objects. This would be a good candidate for a constraint.

We can create a `HasId` type that enforces the presence of an `id` property:

```tsx
type HasId = {
  id: number;
};

type ResourceStatus<TContent extends HasId, TMetadata extends object = {}> =
  | {
      status: "available";
      content: TContent;
      metadata: TMetadata;
    }
  | {
      status: "unavailable";
      reason: string;
    };
```

With these changes in place, it is now required that the `TContent` type parameter must include an `id` property. The `TMetadata` type parameter is optional, but if it is provided it must be an object.

When we try to create a type with `ResourceStatus` that doesn't have an `id` property, TypeScript will raise an error that tells us exactly what's wrong:

```ts twoslash
// @errors: 2344
type HasId = {
  id: number;
};

type ResourceStatus<TContent extends HasId, TMetadata extends object = {}> =
  | {
      status: "available";
      content: TContent;
      metadata: TMetadata;
    }
  | {
      status: "unavailable";
      reason: string;
    };

// ---cut---
type StreamingPlaylist = ResourceStatus<
  {
    name: string;
    tracks: string[];
  },
  {
    creator: string;
    artwork: string;
    dateUpdated: Date;
  }
>;
```

Once the `id` property is added to the `TContent` type parameter, the error will go away.

#### Constraints Describe Required Properties

Note that these constraints we're providing here are just descriptions for properties the object must contain. We can pass `name` and `tracks` into `TContent` as long as it has an `id` property.

In other words, these constraints are _open_, not _closed_. 
You won't get excess property warnings here. 
Any excess properties you pass in will be added to the type.

#### `extends`, `extends`, `extends`

有四種用法

By now, we've seen `extends` used in a few different contexts:

- In generic types, to set constraints on type parameters  
	- 加上限制
- In classes, to extend another class
	- 擴展
- In interfaces, to extend another interface
	- 擴展

There is even another use for `extends` - conditional types, which we'll look at later in this chapter.

**One of TypeScript's annoying habits is that it tends to reuse the same keywords in different contexts.** 
**So it's important to understand that `extends` means different things in different places.**

## Template Literal Types in TypeScript


JS, you can use Template Literal to put var into string
TS, you can use Template Literal to put val unto type


For example, let's create a `PngFile` type that represents a string that ends with ".png":

```tsx
type PngFile = `${string}.png`;
```

Now when we type a new variable as `PngFile`, it must end with ".png":

```tsx
let myImage: PngFile = "my-image.png"; // OK
```

When a string does not match the pattern defined in the `PngFile` type, TypeScript will raise an error:

```ts twoslash
// @errors: 2322
type PngFile = `${string}.png`;

// ---cut---
let myImage: PngFile = "my-image.jpg";
```

Enforce specific string formats with template literal types can be useful in your applications.

### Combining Template Literal Types with Union Types

Template literal types become even more powerful when combined with union types. By passing a union to a template literal type, you can generate a type that represents all possible combinations of the union.

For example, let's imagine we have a set of colors each with possible shades from `100` to `900`:

```tsx
type ColorShade = 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900;
type Color = "red" | "blue" | "green";
```

If we want a combination of all possible colors and shades, we can use a template literal type to generate a new type:

```tsx
type ColorPalette = `${Color}-${ColorShade}`;
```

Now, `ColorPalette` will represent all possible combinations of colors and shades:

```tsx
let myColor: ColorPalette = "red-500"; // OK
let myColor2: ColorPalette = "blue-900"; // OK
```

That's 27 possible combinations - three colors times nine shades.

如果你有某種 string pattern, **template literal type** is your good friend


### Transforming String Types
built in util to transfrom string type
`Uppercase` and `Lowercase`

```ts twoslash
type UppercaseHello = Uppercase<"hello">;
//   ^?
type LowercaseHELLO = Lowercase<"HELLO">;
//   ^?
```


`Capitalize`

```ts twoslash
type CapitalizeMatt = Capitalize<"matt">;
//   ^?
```

`Uncapitalize`

```ts twoslash
type UncapitalizePHD = Uncapitalize<"PHD">;
//   ^?
```



## Conditional Types

use conditional types in TypeScript to create if/else logic in your types

Let's imagine we create a `ToArray` generic type that converts a type to an array type:

```tsx
type ToArray<T> = T[];
```


```ts twoslash


// // 這邊有一個 bug, 如果傳入的已經是 array type, 就會回傳 array of array type, 但我們希望還是 array type
type ToArray<T> = T[];
// ---cut---
type Example = ToArray<string>;
//   ^?  array

type Example2 = ToArray<string[]>;
//   ^?  會是 arr of arr type
```
![[IMG-15 design your type-20241003104933524.png]]

這個時候就是需要用 conditional type:
```tsx
type ToArray<T> = T extends any[] ? T : T[];
```


```tsx
type ToArray<T> = T extends any[] ? T : T[];
//                ^^^^^^^^^^^^^^^   ^   ^^^
//                condition       true/false
```


## Mapped Types

透過 iterate over key and value 來建立 new type


Consider this `Album` interface:
```tsx
interface Album {
  name: string;
  artist: string;
  songs: string[];
}
```

我們想要從上面製造出下面這個type

```tsx
type AlbumWithNullable = {
  name?: string | null;
  artist?: string | null;
  songs?: string[] | null;
};
```

Let's start by, instead of repeating the properties, using a mapped type:

```tsx
type AlbumWithNullable = {
  [K in keyof Album]: K;
};


// Hovering 上面會看到下面...
type AlbumWithNullable = {
  name: "name";
  artist: "artist";
  songs: "songs";
};

```


We can see that `K` represents the _currently iterated key_. 
This means we can use it to get the type of the original `Album` property using an indexed access type:
```tsx
type AlbumWithNullable = {
  [K in keyof Album]: Album[K];
};

// Hovering 上面會看到下面...
type AlbumWithNullable = {
  name: string;
  artist: string;
  songs: string[];
};
```



加上 `| null` to each property:
```tsx
type AlbumWithNullable = {
  [K in keyof Album]: Album[K] | null;
};

// Hovering 上面會看到下面...
type AlbumWithNullable = {
  name: string | null;
  artist: string | null;
  songs: string[] | null;
};
```



To make each property optional. 
We can do this by adding a `?` after the key:

```tsx
type AlbumWithNullable = {
  [K in keyof Album]?: Album[K] | null;
};

// Hovering 上面會看到下面...
type AlbumWithNullable = {
  name?: string | null;
  artist?: string | null;
  songs?: string[] | null;
};
```



Let's make this behavior reusable by wrapping it in a generic type,`Nullable<T>`:

```tsx
type Nullable<T> = {
  [K in keyof T]?: T[K] | null;
};

type AlbumWithNullable = Nullable<Album>;
```

Mapping type 用來從一個object type as input, output 另一個 object type 的情境特別好用


### Key Remapping with `as`

In the previous example, we didn't need to change the key of the object we were iterating over. But what if we did?

Let's say we want to create a new type that has the same properties as `Album`, but with the key names uppercased. We could try using `Uppercase` on `keyof Album`:

```ts twoslash
// @errors: 2536
interface Album {
  name: string;
  artist: string;
  songs: string[];
}

// below have type error
// We can't use `K` to index `Album` because `K` has already been transformed to its uppercase version
type AlbumWithUppercaseKeys = {
  [K in Uppercase<keyof Album>]: Album[K];
};


// 如何解?
// using the `as` keyword to remap the key
// `as` allows us to remap the key while keeping the original key accessible in the loop. 
type AlbumWithUppercaseKeys = {
  [K in keyof Album as Uppercase<K>]: Album[K];
};


// Hovering over AlbumWithUppercaseKeys shows:
type AlbumWithUppercaseKeys = {
  NAME: string;
  ARTIST: string;
  SONGS: string[];
};
```

**這跟 type assertion 的 as 不同喔，這是另一個用法 (但是同一個 keyword! damn! TS!)**


### Using Mapped Types with Union Types

不只是可以 遍歷 object, 也可以遍歷 union 


```tsx
type Example = "a" | "b" | "c";
```

Then, we can create a `MappedExample` type that maps over `Example` and returns the same values:

```tsx
type MappedExample = {
  [E in Example]: E;
};

// hovering over MappedExample shows:
type MappedExample = {
  a: "a";
  b: "b";
  c: "c";
};
```

This chapter should give you a good understanding of advanced methods for designing your types in TypeScript. 
By using generic types, template literal types, conditional types, and mapped types, you can create expressive and reusable types that reflect the business logic of your application.



# ==== 看到這裡 ====
## Exercises

### Exercise 1: Create a `DataShape` Type Helper

Consider the types `UserDataShape` and `PostDataShape`:

```tsx
type ErrorShape = {
  error: {
    message: string;
  };
};

type UserDataShape =
  | {
      data: {
        id: string;
        name: string;
        email: string;
      };
    }
  | ErrorShape;

type PostDataShape =
  | {
      data: {
        id: string;
        title: string;
        body: string;
      };
    }
  | ErrorShape;
```

Looking at these types, they both share a consistent pattern. Both `UserDataShape` and `PostDataShape` possess a `data` object and an `error` shape, with the `error` shape being identical in both. The only difference between the two is the `data` object, which holds different properties for each type.

Your task is to create a generic `DataShape` type to reduce duplication in the `UserDataShape` and `PostDataShape` types.

<Exercise title="Exercise 1: Create a `DataShape` Type Helper" filePath="/src/083-designing-your-types/204-intro-to-generic-types.problem.ts"></Exercise>

### Exercise 2: Typing `PromiseFunc`

This `PromiseFunc` type represents a function that returns a promise:

```tsx
type PromiseFunc = (input: any) => Promise<any>;
```

Provided here are two example tests that use the `PromiseFunc` type with different inputs that currently have errors:

```ts twoslash
// @errors: 2315
import { Equal, Expect } from "@total-typescript/helpers";
type PromiseFunc = (input: any) => Promise<any>;

// ---cut---
type Example1 = PromiseFunc<string, string>;

type test1 = Expect<Equal<Example1, (input: string) => Promise<string>>>;

type Example2 = PromiseFunc<boolean, number>;

type test2 = Expect<Equal<Example2, (input: boolean) => Promise<number>>>;
```

The error messages inform us that the `PromiseFunc` type is not generic. We're also expecting the `PromiseFunc` type to take in two type arguments: the input type and the return type of the promise.

Your task is to update `PromiseFunc` so that both of the tests pass without errors.

<Exercise title="Exercise 2: Typing `PromiseFunc`" filePath="/src/083-designing-your-types/205-multiple-type-parameters.problem.ts"></Exercise>

### Exercise 3: Working with the `Result` Type

Let's say we have a `Result` type that can either be a success or an error:

```tsx
type Result<TResult, TError> =
  | {
      success: true;
      data: TResult;
    }
  | {
      success: false;
      error: TError;
    };
```

We also have the `createRandomNumber` function that returns a `Result` type:

```ts twoslash
// @errors: 2314
type Result<TResult, TError> =
  | {
      success: true;
      data: TResult;
    }
  | {
      success: false;
      error: TError;
    };
// ---cut---
const createRandomNumber = (): Result<number> => {
  const num = Math.random();

  if (num > 0.5) {
    return {
      success: true,
      data: 123,
    };
  }

  return {
    success: false,
    error: new Error("Something went wrong"),
  };
};
```

Because there's only a `number` being sent as a type argument, we have an error message. We're only specifying the number because it can be a bit of a hassle to always specify both the `success` and `error` types whenever we use the `Result` type.

It would be easier if we could designate `Error` type as the default type for `Result`'s `TError`, since that's what most errors will be typed as.

Your task is to adjust the `Result` type so that `TError` defaults to type `Error`.

<Exercise title="Exercise 3: Working with the `Result` Type" filePath="/src/083-designing-your-types/207-default-type-parameters.problem.ts"></Exercise>

### Exercise 4: Constraining the `Result` Type

After updating the `Result` type to have a default type for `TError`, it would be a good idea to add a constraint on the shape of what's being passed in.

Here are some examples of using the `Result` type:

```ts twoslash
// @errors: 2578
type Result<TResult, TError = Error> =
  | {
      success: true;
      data: TResult;
    }
  | {
      success: false;
      error: TError;
    };

// ---cut---

type BadExample = Result<
  { id: string },
  // @ts-expect-error Should be an object with a message property
  string
>;

type GoodExample = Result<{ id: string }, TypeError>;
type GoodExample2 = Result<{ id: string }, { message: string; code: number }>;
type GoodExample3 = Result<{ id: string }, { message: string }>;
type GoodExample4 = Result<{ id: string }>;
```

The `GoodExample`s should pass without errors, but the `BadExample` should raise an error because the `TError` type is not an object with a `message` property. Currently, this isn't the case as seen by the error under the `@ts-expect-error` directive.

Your task is to add a constraint to the `Result` type that ensures the `BadExample` test raises an error, while the `GoodExample`s pass without errors.

<Exercise title="Exercise 4: Constraining the `Result` Type" filePath="/src/083-designing-your-types/208-type-parameter-constraints.problem.ts"></Exercise>

### Exercise 5: A Stricter `Omit` Type

Earlier in the book, we worked with the `Omit` type helper, which allows you to create a new type that omits specific properties from an existing type.

Interestingly, the `Omit` helper also lets you try to omit keys that don't exist in the type you're trying to omit from.

In this example, we're trying to omit the property `b` from a type that only has the property `a`:

```tsx
type Example = Omit<{ a: string }, "b">;
```

Since `b` isn't a part of the type, you might anticipate TypeScript would show an error, but it doesn't.

Instead, we want to implement a `StrictOmit` type that only accepts keys that exist in the provided type. Otherwise, an error should be shown.

Here's the start of `StrictOmit`, which currently has an error under `K`:

```ts twoslash
// @errors: 2344
type StrictOmit<T, K> = Omit<T, K>;
```

Currently, the `StrictOmit` type behaves the same as a regular `Omit` as evidenced by this failing `@ts-expect-error` directive:

```ts twoslash
// @errors: 2344 2578
type StrictOmit<T, K> = Omit<T, K>;
// ---cut---
type ShouldFail = StrictOmit<
  { a: string },
  // @ts-expect-error
  "b"
>;
```

Your task is to update `StrictOmit` so that it only accepts keys that exist in the provided type `T`. If a non-valid key `K` is passed, TypeScript should return an error.

Here are some tests to show how `StrictOmit` should behave:

```tsx
type tests = [
  Expect<Equal<StrictOmit<{ a: string; b: number }, "b">, { a: string }>>,
  Expect<Equal<StrictOmit<{ a: string; b: number }, "b" | "a">, {}>>,
  Expect<
    Equal<StrictOmit<{ a: string; b: number }, never>, { a: string; b: number }>
  >,
];
```

You'll need to remember `keyof` and how to constraint type parameters to complete this exercise.

<Exercise title="Exercise 5: A Stricter `Omit` Type" filePath="/src/083-designing-your-types/209-tighter-version-of-omit.problem.ts"></Exercise>

### Exercise 6: Route Matching

Here we have a `route` typed as `AbsoluteRoute`:

```tsx
type AbsoluteRoute = string;

const goToRoute = (route: AbsoluteRoute) => {
  // ...
};
```

We're expecting that the `AbsoluteRoute` will represent any string that has a forward slash at the start of it. For example, we'd expect the following strings to be valid `route`s:

```tsx
goToRoute("/home");
goToRoute("/about");
goToRoute("/contact");
```

However, if we attempt passing a string that doesn't start with a forward slash there currently is not an error:

```ts twoslash
// @errors: 2578
type AbsoluteRoute = string;

const goToRoute = (route: AbsoluteRoute) => {
  // ...
};
// ---cut---
goToRoute(
  // @ts-expect-error
  "somewhere",
);
```

Because `AbsoluteRoute` is currently typed as `string`, TypeScript fails to flag this as an error.

Your task is to refine the `AbsoluteRoute` type to accurately expect that strings begin with a forward slash.

<Exercise title="Exercise 6: Route Matching" filePath="/src/083-designing-your-types/210-template-literal-types.problem.ts"></Exercise>

### Exercise 7: Sandwich Permutations

In this exercise, we want to build a `Sandwich` type.

This `Sandwich` is expected to encompass all possible combinations of three types of bread (`"rye"`, `"brown"`, `"white"`) and three types of filling (`"cheese"`, `"ham"`, `"salami"`):

```tsx
type BreadType = "rye" | "brown" | "white";

type Filling = "cheese" | "ham" | "salami";

type Sandwich = unknown;
```

As seen in the test, there are several possible combinations of bread and filling:

```tsx
type tests = [
  Expect<
    Equal<
      Sandwich,
      | "rye sandwich with cheese"
      | "rye sandwich with ham"
      | "rye sandwich with salami"
      | "brown sandwich with cheese"
      | "brown sandwich with ham"
      | "brown sandwich with salami"
      | "white sandwich with cheese"
      | "white sandwich with ham"
      | "white sandwich with salami"
    >
  >,
];
```

Your task is to use a template literal type to define the `Sandwich` type. This can be done in just one line of code!

<Exercise title="Exercise 7: Sandwich Permutations" filePath="/src/083-designing-your-types/211-passing-unions-to-template-literal-types.problem.ts"></Exercise>

### Exercise 8: Attribute Getters

Here we have an `Attributes` interface, that contains a `firstName`, `lastName`, and `age`:

```tsx
interface Attributes {
  firstName: string;
  lastName: string;
  age: number;
}
```

Following that, we have `AttributeGetters` which is currently typed as `unknown`:

```tsx
type AttributeGetters = unknown;
```

As seen in the tests, we expect `AttributeGetters` to be an object composed of functions. When invoked, each of these functions should return a value matching the key from the `Attributes` interface:

```ts twoslash
// @errors: 2344
import { Equal, Expect } from "@total-typescript/helpers";

interface Attributes {
  firstName: string;
  lastName: string;
  age: number;
}

type AttributeGetters = unknown;
// ---cut---
type tests = [
  Expect<
    Equal<
      AttributeGetters,
      {
        firstName: () => string;
        lastName: () => string;
        age: () => number;
      }
    >
  >,
];
```

Your task is to define the `AttributeGetters` type so that it matches the expected output. To do this, you'll need to iterate over each key in `Attributes` and produce a function as a value which then returns the value of that key.

<Exercise title="Exercise 8: Attribute Getters" filePath="/src/083-designing-your-types/212-mapped-types.problem.ts"></Exercise>

### Exercise 9: Renaming Keys in a Mapped Type

After creating the `AttributeGetters` type in the previous exercise, it would be nice to rename the keys to be more descriptive.

Here's a test case for `AttributeGetters` that currently has an error:

```tsx
type tests = [
  Expect<
    Equal<
      AttributeGetters,
      {
        getFirstName: () => string;
        getLastName: () => string;
        getAge: () => number;
      }
    >
  >,
];
```

Your challenge is to adjust the `AttributeGetters` type to remap the keys as specified. You'll need to use the `as` keyword, template literals, as well as TypeScript's built-in `Capitalize<string>` type helper.

<Exercise title="Exercise 9: Renaming Keys in a Mapped Type" filePath="/src/083-designing-your-types/213-as-in-mapped-types.problem.ts"></Exercise>

### Solution 1: Create a `DataShape` Type Helper

Here's how a generic `DataShape` type would look:

```tsx
type DataShape<TData> =
  | {
      data: TData;
    }
  | ErrorShape;
```

With this type defined, the `UserDataShape` and `PostDataShape` types can be updated to use it:

```tsx
type UserDataShape = DataShape<{
  id: string;
  name: string;
  email: string;
}>;

type PostDataShape = DataShape<{
  id: string;
  title: string;
  body: string;
}>;
```

### Solution 2: Typing `PromiseFunc`

The first thing we need to do is make `PromiseFunc` generic by adding type parameters to it.

In this case, since we want it to have two parameters we call them `TInput` and `TOutput` and separate them with a comma:

```tsx
type PromiseFunc<TInput, TOutput> = (input: any) => Promise<any>;
```

Next, we need to replace the `any` types with the type parameters.

In this case, the `input` will use the `TInput` type, and the `Promise` will use the `TOutput` type:

```tsx
type PromiseFunc<TInput, TOutput> = (input: TInput) => Promise<TOutput>;
```

With these changes in place, the errors go away and the tests pass because `PromiseFunc` is now a generic type. Any type passed as `TInput` will serve as the input type, and any type passed as `TOutput` will act as the output type of the Promise.

### Solution 3: Working with the `Result` Type

Similar to other times you set default values, the solution is to use an equals sign.

In this case, we'll add the `=` after the `TError` type parameter, and then specify `Error` as the default type:

```tsx
type Result<TResult, TError = Error> =
  | {
      success: true;
      data: TResult;
    }
  | {
      success: false;
      error: TError;
    };
```

`Result` types are a great way to ensure errors are handled properly. For instance, `result` here must be checked for success before accessing the `data` property:

```tsx
const result = createRandomNumber();

if (result.success) {
  console.log(result.data);
} else {
  console.error(result.error.message);
}
```

This pattern can be a great alternative to `try...catch` blocks in JavaScript.

### Solution 4: Constraining the `Result` Type

We want to set a constraint on `TError` to ensure that it is an object with a `message` string property, while also retaining `Error` as the default type for `TError`.

To do this, we'll use the `extends` keyword for `TError` and specify the object with a `message` property as the constraint:

```tsx
type Result<TResult, TError extends { message: string } = Error> =
  | {
      success: true;
      data: TResult;
    }
  | {
      success: false;
      error: TError;
    };
```

Now if we remove the `@ts-expect-error` directive from `BadExample`, we will get an error under `string`:

```ts twoslash
// @errors: 2344
type Result<TResult, TError extends { message: string } = Error> =
  | {
      success: true;
      data: TResult;
    }
  | {
      success: false;
      error: TError;
    };

// ---cut---
type BadExample = Result<{ id: string }, string>;
```

The behavior of constraining type parameters and adding defaults is similar to runtime parameters. However, unlike runtime arguments, you can add additional properties and still satisfy the constraint:

```tsx
type GoodExample2 = Result<{ id: string }, { message: string; code: number }>;
```

A runtime argument constraint would be limited only containing a `message` string property without any additional properties, so the above wouldn't work the same way.

### Solution 5: A Stricter `Omit` Type

Here's the starting point of the `StrictOmit` type and the `ShouldFail` example that we need to fix:

```ts twoslash
// @errors: 2344 2578
type StrictOmit<T, K> = Omit<T, K>;

type ShouldFail = StrictOmit<
  { a: string },
  // @ts-expect-error
  "b"
>;
```

Our goal is to update `StrictOmit` so that it only accepts keys that exist in the provided type `T`. If a non-valid key `K` is passed, TypeScript should return an error.

Since the `ShouldFail` type has a key of `a`, we'll start by updating the `StrictOmit`'s `K` to extend `a`:

```tsx
type StrictOmit<T, K extends "a"> = Omit<T, K>;
```

Removing the `@ts-expect-error` directive from `ShouldFail` will now show an error under `"b"`:

```ts twoslash
// @errors: 2344
type StrictOmit<T, K extends "a"> = Omit<T, K>;
// ---cut---
type ShouldFail = StrictOmit<{ a: string }, "b">;
```

This shows us that the `ShouldFail` type is failing as expected.

However, we want to make this more dynamic by specifying that `K` will extend any key in the object `T` that is passed in.

We can do this by changing the constraint from `"a"` to `keyof T`:

```tsx
type StrictOmit<T, K extends keyof T> = Omit<T, K>;
```

Now in the `StrictOmit` type, `K` is bound to extend the keys of `T`. This imposes a limitation on the type parameter `K` that it must belong to the keys of `T`.

With this change, all of the tests pass as expected with any keys that are passed in:

```tsx
type tests = [
  Expect<Equal<StrictOmit<{ a: string; b: number }, "b">, { a: string }>>,
  Expect<Equal<StrictOmit<{ a: string; b: number }, "b" | "a">, {}>>,
  Expect<
    Equal<StrictOmit<{ a: string; b: number }, never>, { a: string; b: number }>
  >,
];
```

### Solution 6: Route Matching

In order to specify that `AbsoluteRoute` is a string that begins with a forward slash, we'll use a template literal type.

Here's how we could create a type that represents a string that begins with a forward slash, followed by either "home", "about", or "contact":

```tsx
type AbsoluteRoute = `/${"home" | "about" | "contact"}`;
```

With this setup our tests would pass, but we'd be limited to only those three routes.

Instead, we want to allow for any string that begins with a forward slash.

To do this, we can just use the `string` type inside of the template literal:

```tsx
type AbsoluteRoute = `/${string}`;
```

With this change, the `somewhere` string will cause an error since it does not begin with a forward slash:

```tsx
goToRoute(
  // @ts-expect-error
  "somewhere",
);
```

### Solution 7: Sandwich Permutations

Following the pattern of the tests, we can see that the desired results are named:

```
bread "sandwich with" filling
```

That means we should pass the `BreadType` and `Filling` unions to the `Sandwich` template literal with the string `"sandwich with"` in between:

```tsx
type BreadType = "rye" | "brown" | "white";
type Filling = "cheese" | "ham" | "salami";
type Sandwich = `${BreadType} sandwich with ${Filling}`;
```

TypeScript generates all the possible combinations, leading to the type `Sandwich` being:

```tsx
| "rye sandwich with cheese"
| "rye sandwich with ham"
| "rye sandwich with salami"
| "brown sandwich with cheese"
| "brown sandwich with ham"
| "brown sandwich with salami"
| "white sandwich with cheese"
| "white sandwich with ham"
| "white sandwich with salami"
```

### Solution 8: Attribute Getters

Our challenge is to derive the shape of `AttributeGetters` based on the `Attributes` interface:

```tsx
interface Attributes {
  firstName: string;
  lastName: string;
  age: number;
}
```

To do this, we'll use a mapped type. We'll start by using `[K in keyof Attributes]` to iterate over each key in `Attributes`. Then, we'll create a new property for each key, which will be a function that returns the type of the corresponding property in `Attributes`:

```tsx
type AttributeGetters = {
  [K in keyof Attributes]: () => Attributes[K];
};
```

The `Attributes[K]` part is the key to solving this challenge. It allows us to index into the `Attributes` object and return the actual values of each key.

With this approach, we get the expected output and all tests pass as expected:

```tsx
type tests = [
  Expect<
    Equal<
      AttributeGetters,
      {
        firstName: () => string;
        lastName: () => string;
        age: () => number;
      }
    >
  >,
];
```

### Solution 9: Renaming Keys in a Mapped Type

Our goal is to create a new mapped type, `AttributeGetters`, that changes each key in `Attributes` into a new key that begins with "get" and capitalizes the original key. For example, `firstName` would become `getFirstName`.

Before we get to the solution, let's look at an incorrect approach.

#### The Incorrect Approach

It might be tempting to think that you should transform `keyof Attributes` before it even gets to the mapped type.

To do this, we'd creating a `NewAttributeKeys` type and setting it to a template literal with `keyof Attributes` inside of it added to `get`:

```tsx
type NewAttributeKeys = `get${keyof Attributes}`;
```

However, hovering over `NewAttributeKeys` shows that it's not quite right:

```tsx
// hovering over NewAttributeKeys shows:
type NewAttributeKeys = "getfirstName" | "getlastName" | "getage";
```

Adding the global `Capitalize` helper results in the keys being formatted correctly:

```tsx
type NewAttributeKeys = `get${Capitalize<keyof Attributes>}`;
```

Since we have formatted keys, we can now use `NewAttributeKeys` in the map type:

```ts twoslash
// @errors: 2536
interface Attributes {
  firstName: string;
  lastName: string;
  age: number;
}
type NewAttributeKeys = `get${Capitalize<keyof Attributes>}`;

// ---cut---
type AttributeGetters = {
  [K in NewAttributeKeys]: () => Attributes[K];
};
```

However, we have a problem. We can't use `K` to index `Attributes` because each `K` has changed and no longer exists on `Attributes`.

We need to maintain access to the original key inside the mapped type.

#### The Correct Approach

In order to maintain access to the original key, we can use the `as` keyword.

Instead of using the `NewAttributeKeys` type we tried before, we can update the map type to use `keyof Attributes as` followed by the transformation we want to make:

```tsx
type AttributeGetters = {
  [K in keyof Attributes as `get${Capitalize<K>}`]: () => Attributes[K];
};
```

We now iterate over each key `K` in `Attributes`, and use it in a template literal type that prefixes "get" and capitalizes the original key. Then the value paired with each new key is a function that returns the type of the original key in `Attributes`.

Now when we hover over the `AttributeGetters` type, we see that it's correct and the tests pass as expected:

```tsx
// hovering over AttributeGetters shows:
type AttributeGetters = {
  getFirstName: () => string;
  getLastName: () => string;
  getAge: () => number;
};
```