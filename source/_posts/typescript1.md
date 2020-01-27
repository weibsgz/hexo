---
title: 正则学习
date: 2020-01-27 14:13:23
tags: js
category: "js"
---

```
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
