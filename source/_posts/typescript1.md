---
title: typescript 基础
date: 2020-01-27 14:13:23
tags: js
category: "js"
---

```

// 参数解构
function demo({ first, second }: { first: number; second: number }) {
  return first + second;
}

console.log(demo({ first: 1, second: 2 }));

//数组
const arr: (number | string)[] = [1, 2, "3"];

type User = {
  name: string;
  age: number;
};
const objArr: User[] = [
  {
    name: "dell",
    age: 18
  }
];

//type 指定传参可以是函数
type fn = () => string
function bbb(n:string|fn ) {
    return 1
}

bbb(function(){return ''})

//元组 tuple 数组中个数，和每个类型是固定的
const teacherInfo: [string, string, number] = ["dell", "male", 18];

const teacherList: [string, string, number][] = [
  ["dell", "male", 18],
  ["boby", "male", 33],
  ["jenny", "female", 39]
];

//interface 和 type类型别名的区别是 type也可以表达基础数据类型
//比如：type person1 = string
interface Person {
  name: string;
  age?: number;
  [propName: string]: any; //今后可能会有不可预知的属性 只要KEY是STRING，值是ANY就可以
  //如果不写 下边我传sex会报错
  say(): string;
}

const getName = (person: Person) => {
  return person.name;
};

const setName = (person: Person, name: string): void => {
  person.name = name;
};

let na = getName({
  name: "weibin",
  age: 18,
  sex: "male", //如果接口没有定义  [propName: string]: any; 会报错 因为未特殊指明有sex
  say() {
    return "bark";
  }
});

console.log(na);

//类应用接口  implements是对接口的实现 作用就是interface约定好接口的内容 各端自己实现
class somebody implements Person {
  name = "dell";
  say() {
    return "hello";
  }
}

//接口继承
interface Teacher extends Person {
  teach(): void;
}

//定义函数类型的接口
interface SayHi {
  (word: string): string;
}

const say: SayHi = (word: string) => {
  return word;
};

//单列

class Demo {
  private static instance: Demo;
  //public name: string 这个参数的写法相当于构造函数写了 this.name = name;
  private constructor(public name: string) {}
  //static 可以方法直接挂在到类上 而不是类的实例上
  static getInstance(name: string) {
    if (!this.instance) {
      this.instance = new Demo(name);
    }
    return this.instance;
  }
}

//let demo1 = new Demo()  //报错 因为先执行构造函数 private 外部无法执行

const demo1 = Demo.getInstance("weibin");
console.log(demo1.name);



 //联合类型
interface Bird {
  fly: Boolean;
  sing: () => {};
}

interface Dog {
  fly: Boolean;
  bark: () => {};
}

//animal 是无法知道你要调用的有sing方法还是bark方法 只能通过断言
//或者判断写 if('sing' in animal)
function trainAnimal(animal: Bird | Dog) {
  if (animal.fly) {
    (animal as Bird).sing();
  } else {
    (animal as Dog).bark();
  }
}

//用typeof形式做类型保护
function add(first: string | number, second: string | number) {
  if (typeof first === "string" || typeof second === "string") {
    return `${first}${second}`;
  }
  return first + second;
}

//instanceof
class NumberObj {
  count = 10;
}

function addSecond(first: object | NumberObj, second: object | NumberObj) {
  if (first instanceof NumberObj && second instanceof NumberObj) {
    return first.count + second.count;
  }
}

//枚举类型 enum

enum Status {
  Offline = 2,
  Online,
  error
}
console.log(Status.Offline, Status.Online, Status.error); // 2,3,4 默认 0,1,2
console.log(Status[2]); //Offline

//泛型  比如一个函数 传入的两个参数必须是一致的类型

function join<ABC>(first: ABC, second: ABC) {
  return `${first}${second}`;
}
//join(1, "2"); //报错
join(1, 2);

//T 类型的数组
class DataManager<T> {
  constructor(private data: T[]) {
    // this.data = data;
  }
  getItem(index: number) {
    console.log(this.data[index]);
  }
}

const data = new DataManager(["1", "2"]);
data.getItem(1);

//泛型继承 泛型必须有一个name属性
//如果指定泛型必须是一个string 或者 number 也可以写成 T extends number | string
interface item {
  name: string;
}

class DataManager2<T extends item> {
  constructor(private data: T[]) {}
  getItem(index: number) {
    console.log(this.data[index].name);
  }
}

const data2 = new DataManager2([
  {
    name: "weibin"
  }
]);
data2.getItem(0);


//如果要需要返回参数的长度，又不能指定为数组 （T[]）因为字符串也可以返回长度
//可以借助接口
interface IwithLength {
  length: number
}

function echoWithLength<T extends IwithLength>(arg: T): T {
  console.log(arg.length)
  return arg
}

//内置类型
interface Ipserson {
  name: string
  age: number
}

//正常必须写2个属性
let a: Ipserson = { name: 'weibin', age: 10 }

//使用 Partial内置类型 可以让所有选项变为可选
type Ipartial = Partial<Ipserson>
let a1: Ipartial = { name: 'weibin' }


//如果有多个命名空间的引用
///<reference path="components.ts" />
//命名空间 就是把方法包裹起来不污染全局，如果内部哪个方法想暴露出去就用export

namespace Home {
  export class Page {
    constructor() {
      new Components.Header();
    }
  }
}

//另一个命名空间 components.ts

// namespace Components {
//   export class Header {
//     constructor() {
//       const elem = document.createElement("div");
//       elem.innerText = "This is Header";
//       document.body.appendChild(elem);
//     }
//   }
// }


//方法装饰器 首先装饰器需要打开tsconfig.json的配置 experimentalDecorators/emitDecoratorMetadata
//装饰器都是函数
//方法装饰器 如果是普通方法 target对应调用类的prototype
//如果是静态方法 target 对应的是类的构造函数
//key对应的是方法名
//descriptor.value对应的是方法本身
const user: any = undefined;
//用工厂方法可以穿参
function catchError(msg: string) {
  return function(target: any, key: string, descriptor: PropertyDescriptor) {
    //打印出 Test { getName: [Function], getAge: [Function] } 'getName' [Function]
    console.log(target, key, descriptor.value);
    //重写getName,getAge方法
    //这里要重新制定下当前方法 要不下边直接用descriptor.value()会报错
    const fn = descriptor.value;
    descriptor.value = function() {
      try {
        fn();
      } catch (e) {
        console.log(msg);
      }
    };
  };
}
class Test {
  @catchError("userInfo.name 不存在")
  getName() {
    return user.name;
  }
  @catchError("userInfo.age 不存在")
  getAge() {
    try {
      return new user.age();
    } catch (e) {
      console.log("user.age bu cun zai");
    }
  }
}

const test = new Test();
test.getName();

```

### tsconfig.json 配置

```
{
  /* 只编译当前目录下的 demo.ts文件 如果没有写这个 include */
  /*那么tsc命令就是编译根目录下所有的TS文件(如果tsc后面跟具体文件名就不会应用这个tsconfig.json文件)*/
  //"include": ["./demo.ts"],
  // "exclude": [],
  "compilerOptions": {
    // "incremental": true,     /* 增量编译，之前编译过的不会编译 会生成一个文件记录编译信息*/
    "target": "es5" /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */,
    "module": "commonjs" /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */,
    // "lib": [],                             /* Specify library files to be included in the compilation. */
    "allowJs": true /* 允许JS文件被编译 */,
    "checkJs": true /* 检测JS报错 */,
    // "jsx": "preserve",                     /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
    // "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    // "declarationMap": true,                /* Generates a sourcemap for each corresponding '.d.ts' file. */
    // "sourceMap": true,                     /* Generates corresponding '.map' file. */
    //"outFile": "./build/page.js" /* 输出打包到一个文件.如果指定的话就不支持commonjs格式 一般把module改为 AMD */,
    "outDir": "./build" /* 编译后的输出目录 */,
    "rootDir": "./" /* 指定要编译的目录 */,
    // "composite": true,                     /* Enable project compilation */
    // "tsBuildInfoFile": "./",               /* Specify file to store incremental compilation information */
    "removeComments": true /* 删除注释. */,
    // "noEmit": true,                        /* Do not emit outputs. */
    // "importHelpers": true,                 /* Import emit helpers from 'tslib'. */
    // "downlevelIteration": true,            /* Provide full support for iterables in 'for-of', spread, and destructuring when targeting 'ES5' or 'ES3'. */
    // "isolatedModules": true,               /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */

    /* Strict Type-Checking Options */
    "strict": true /* Enable all strict type-checking options. */,
    // "noImplicitAny": true,                 /* Raise error on expressions and declarations with an implied 'any' type. */
    "strictNullChecks": true /* null 是否可以作为赋值给其他基础类型  */,
    // "strictFunctionTypes": true,           /* Enable strict checking of function types. */
    // "strictBindCallApply": true,           /* Enable strict 'bind', 'call', and 'apply' methods on functions. */
    // "strictPropertyInitialization": true,  /* Enable strict checking of property initialization in classes. */
    // "noImplicitThis": true,                /* Raise error on 'this' expressions with an implied 'any' type. */
    // "alwaysStrict": true,                  /* Parse in strict mode and emit "use strict" for each source file. */

    /* Additional Checks */
    // "noUnusedLocals": true /* 定义的变量没有被使用报错 */,
    //"noUnusedParameters": true /* 定义的参数没有被使用报错 */,
    // "noImplicitReturns": true,             /* Report error when not all code paths in function return a value. */
    // "noFallthroughCasesInSwitch": true,    /* Report errors for fallthrough cases in switch statement. */

    /* Module Resolution Options */
    // "moduleResolution": "node",            /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    // "baseUrl": "./",                       /* 制定根路径 */
    // "paths": {},                           /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    // "rootDirs": [],                        /* List of root folders whose combined content represents the structure of the project at runtime. */
    // "typeRoots": [],                       /* List of folders to include type definitions from. */
    // "types": [],                           /* Type declaration files to be included in compilation. */
    // "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
    "esModuleInterop": true /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */,
    // "preserveSymlinks": true,              /* Do not resolve the real path of symlinks. */
    // "allowUmdGlobalAccess": true,          /* Allow accessing UMD globals from modules. */

    /* Source Map Options */
    // "sourceRoot": "",                      /* Specify the location where debugger should locate TypeScript files instead of source locations. */
    // "mapRoot": "",                         /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,               /* Emit a single file with source maps instead of having a separate file. */
    // "inlineSources": true,                 /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */

    /* Experimental Options */
    // "experimentalDecorators": true,        /* Enables experimental support for ES7 decorators. */
    // "emitDecoratorMetadata": true,         /* Enables experimental support for emitting type metadata for decorators. */

    /* Advanced Options */
    "forceConsistentCasingInFileNames": true /* Disallow inconsistently-cased references to the same file. */
  }
}


```
