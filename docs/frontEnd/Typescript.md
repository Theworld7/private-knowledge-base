# Typescript

## 联合类型

联合类型用 `｜` 分隔符，联合类型产生一个包含所有类型的**选择集**类型。联合类型表示一个值可以是几种类型之一，当一个变量希望传入某种类型时，可以考虑使用联合类型。

```typescript
type IdType = string ｜ number ｜ boolean;

let id: IdType;
```

## 交叉类型

交叉类型用 `&` 分隔符，交叉类型产生一个包含**所有属性**的**新**类型。交叉类型可以更好的帮忙我们进行代码复用。

> 交叉类型在使用的时候有时候会产生一个**新的类型 `never`**,一般产生这种情况是两个 `interface` 使用交叉类型 `&` 进行处理，在 `interface1` 中有一个 `name:string` ,在 `interface2` 中有一个`name:number`，对于这种情况最后产生的新 `interface` ,里面的 `name` 属性类型会变为 `never` 类型 。 **因为没有一个类型即是 `string` 类型又是`number` 类型。**

### 应用例子

1. 写业务，类型拆分解耦

   有一个需求：做一个答题 `PK` 小程序问答记录排行榜的时候

   问题记录`interface`定义

   ```typescript
   interface IQuestionRecord {
     createTime: string;
     userName: string;
     userAvatar: string;
     question: {
       title: string;
       content: string;
       picture: string[];
     };
   }
   ```

   答题记录`interface`定义

   ```typescript
   interface IAnswerRecord {
     createTime: string;
     userName: string;
     userAvatar: string;
     answer: {
       comment: string;
       audio?: {
         url: string;
       };
     };
   }
   ```

   这两个 `interface` 的定义，具有相同的片段，可以根据功能和模块拆分一下重复接口声明：

   ```typescript
   interface IUserBaseInfo {
     createTime: string;
     userName: string;
     userAvatar: string;
   }
   interface IQuestionRecord {
     question: {
       title: string;
       content: string;
       picture: string[];
     };
   }
   interface IAnswerRecord {
     answer: {
       comment: string;
       audio?: {
         url: string;
       };
     };
   }
   ```

   使用交叉类型进行接口混入

   ```typescript
   type Mixin<T, X> = {
     [P in keyof (T & X)]: (T & X)[P];
   };
   // 更简单的写法
   type Mixin<T, X> = T & X;
   
   // 用泛型混入，方便之后还会出现什么数据也带有用户基础信息
   // 方便做拓展和复用
   type MixinUserBaseInfo<T> = Mixin<IUserBaseInfo, T>;
   
   interface IRecordConfig {
     question?: MixinUserBaseInfo<IQuestionRecord>;
     answer?: MixinUserBaseInfo<IAnswerRecord>;
   }
   // 最终使用的时候输列表数据
   export type RecordConfigList = IRecordConfig[];
   ```

2. 处理函数参数

   函数需要定义一些传参，而这些参数的定义可能用到多个函数，有时候是必填参数，有时候是可选参数

   * `IArgsBase` 接口

     ```typescript
     export interface IArgsBase<T>{
       name?:string;
       description?:string;
       visible?:boolean;
       execConf:T:(()=>T);
     }
     ```

   * `RequireArg` 类型

     ```typescript
     export type RequiredArg<T> = IArgsBase<T> & {
       required: true;
       value: T;
     };
     ```

   * `OptionalArg` 类型

     ```typescript
     export type OptionalArg<T> = IArgsBase<T> & {
       required: false;
       value?: T;
     };
     ```

   * `OptionalArg` 类型使用例子

     ```typescript
     interface User {
       val: string;
     }
     const testFun = (args:OptionalArg<User>):User => {
       return {
         val: '好好好',
       };
     }
     testFun({ name: '123', required: true, execConf: { val: '123' } });
     ```

   与例子1异曲同工，只是使用场景有些不同。

   ```typescript
   type Getters<T> = {
     [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
   };
   interface Person {
       name: string;
       age: number;
       location: string;
   }
   type LazyPerson = Getters<Person>;
   // {
   //   getName: () => string;
   //   getAge: () => number;
   //   getLocation: () => string;
   // }
   ```

3. 我们用其他组件库的组件，但是会在他的 props 基础上增加一些我们的 props 属性

   ```typescript
   class CustomModal<T> extends React.Component<ComponentProps & T> {}
   ```

## TS 实现一个 Pick 功能

`Pick<T, K>` 从一个复合类型 `T` 中取出几个想要的属性 `K` ，构造一个新类型。

```typescript
// 定义一个用户信息原始类型
interface IUser {
  name: string;
  age: number;
  number: number;
}
```

还需要定义一个编辑用户新的类型，只需要 `name` 和 `age` 因为 `number` 不可修改

```typescript
interface IEditUser {
  name: string;
  age: number;
}
```

上面的这种写法可以实现需求，但是如果我们修改 `IUser` 中的内容，后面 `IEditUser` 也可能需要修改，并且代码量也很大。 `Pick` 就解决了以上问题

```typescript
type EditUser = Pick<IUser, 'name' | 'age'>;
```

### 源码实现

```typescript
type MyPick<T, K extends keyof T> = { [S in K]: T[S] };
```

### keyof 操作符

`keyof` 用于返回对应类型所有 `Key` 的联合类型。

```typescript
interface IUser {
  name: string;
  age: number;
  number: number;
}
type UserKeys = keyof IUser; // "name" | "age" | "number" 联合类型
```

工作中关于 `keyof` 常用的一个场景。一个函数，接受两个参数，参数一是一个对象，参数二是这个对象中 `key`，如何用 `TypeScript` 编写函数

```typescript
function getValue<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}
```

使用 `keyof` 可以得到所有 `key` 的联合类型，并且 `obj[key]` 会被推断为 `T[K]` 正确的返回值类型，不再是 `any` 类型。

### 拓展 typeof

推断一个 `Interface` 类型

```typescript
interface IUser {
  name: string;
  age: number;
  number: number;
}
type UserType1 = typeof IUser;
let u: UserType1 = {
  name: 'xiao2',
  age: 18,
  number: 001,
};
```

推断一个 `class` 类型

```typescript
class TestClass{
    constructor(public name:string,public age:number){}
}
type Instance = InstanceType<typeof TestClass>;
```

官网中还有个小例子在 `ReturnType` 中使用,推导一个函数的返回值，直接传入函数是有问题的

```typescript
function f() {
  return { x: 10, y: 3 };
}
// 错误使用
type P = ReturnType<f>;
// 报错 'f' refers to a value, but is being used as a type here. Did you mean 'typeof f'?
// 正确使用
type P = ReturnType<typefo f>;
// type p = {x:number,y:number}
```

#### 映射类型语法

```typescript
{[S in K]:T}
```

`in` 操作符用于遍历 `K` 类型中的所有类型。可以把它理解为 for...in

```typescript
type InExample = 'a' | 'b' | 'c' | 'd';
type Obj = {
  [T in InExample]: string; // 遍历InExample，定义每一个key的类型为string
};
上面Obj等价于;
/*type Obj = {
    a:string,
    b:string,
    c:string,
    d:string
}*/
```

`T` 类型变量可以用于表示 `TS` 中的任意类型

在 `Pick` 的实现中，`{[S in K]:T[S]}` 这段实现就有用到映射类型语法,表示遍历类型 `K`中所有的类型。 `T[S]` 可以理解为属性访问的语法，用来获取对象类型某个属性对应值的类型。

#### extends

用来约束类型。 第二个泛型输入的 `Key` 被约束在 `T` 的 `key` 内，如果超过这个范围会报错。

### 实例

#### 实现 Partial

`Partial` 用于修改类型中属性为可选类型

```typescript
type Partial<T> = {[P in keyof T]?:T[P]}
```

#### 实现 Required

`Required` 用于修改类型中可选属性为必填属性

```typescript
type Required<T> ={[P in keyof T]-?:T[P]}
```

#### 实现 ReadOnly

`ReadOnly` 用于修改类型中属性为只读

```typescript
type ReadOnly<T> = {readonly [P in keyof T]:T[P]}
```

## TS 实现一个 Exclude 功能

`Exclude` 用于删除类型集合中的指定类型

```typescript
type Type1 = string | number; // 联合类型
type TypeExclude = Exclude<Type1, string>; // number
```

### 源码实现

```typescript
type Exclude<T, U> = T extends U ? never : T;
```

#### extends 条件类型

```typescript
T extends U ? X : Y
```

条件类型是一种条件表达式进行类型的关系检测，条件类型在理解的时候可以想到类似`JavaScript` 的三元表达式，`T`, `U`, `X` 和 `Y` 代表了任意类型, 如果 `T` 类型可以赋值个类型 `U` ，返回类型 `X` 否则返回类型 `Y`。

条件类型可以结合条件链使用，用来同时判断多种类型：

```typescript
type TypeCheck<T> = T extends string
  ? 'string'
  : T extends number
  ? 'number'
  : T extends boolean
  ? 'boolean'
  : T extends undefined
  ? 'undefined'
  : T extends Function
  ? 'function'
  : 'object';
type T0 = TypeCheck<string>; // "string"
type T1 = TypeCheck<'a'>; // "string"
type T2 = TypeCheck<true>; // "boolean"
type T3 = TypeCheck<() => void>; // "function"
type T4 = TypeCheck<string[]>; // "object"
```

假如传入联合类型会返回什么结果呢？

```typescript
type T10 = TypeCheck<string | (() => void)>; // "string" | "function"
type T11 = TypeCheck<string | string[] | undefined>; // "string" | "object" | "undefined"
```

当传入的被检查类型是联合类型的时候，类型检查过程会被分解为多个分支进行

```typescript
T extends U ? X : Y
T => A | B | C // 假如T是联合类型 A ｜ B ｜ C
A | B | C extends U ? X : Y  =>
(A extends U ? X : Y) | (B extends U ? X : Y) | (C extends U ? X : Y)
```

