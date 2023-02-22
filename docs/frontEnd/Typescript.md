# Typescript

## 联合类型

联合类型用 `｜` 分隔符，联合类型产生一个包含所有类型的**选择集**类型。联合类型表示一个值可以是几种类型之一，当一个变量希望传入某种类型时，可以考虑使用联合类型。

```typescript
type IdType = string ｜ number ｜ boolean;

let id: IdType;
```

## 交叉类型

交叉类型用 `&` 分隔符，交叉类型产生一个包含**所有属性**的**新**类型。交叉类型可以更好的帮忙我们进行代码复用。

> 交叉类型在使用的时候有时候会产生一个新的类型 `never`,一般产生这种情况是两个 `interface` 使用交叉类型 `&` 进行处理，在 `interface1` 中有一个 `name:string` ,在 `interface2` 中有一个`name:number`，对于这种情况最后产生的新 `interface` ,里面的 `name` 属性类型会变为 `never` 类型 。 **因为没有一个类型即是 `string` 类型又是`number` 类型。**

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

