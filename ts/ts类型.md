# ts类型

| 类型      | 例子                    | 描述                |
|:-------:|:---------------------:|:-----------------:|
| number  | 1,2,-1,2.3            | 任意数字              |
| string  | 'hello'               | 任意字符串             |
| boolean | True,false            | 布尔值               |
| 字面量     | 其本身                   | 限制变量的值就是该字面量的值    |
| any     | *                     | 任意类型              |
| unknown | *                     | 类型安全的any          |
| void    | 空值（undefined）         | 没有值（或undefined）   |
| never   | 没有值                   | 不能是任何值            |
| object  | {name: 'xh', age: 18} | 任意的JS对象           |
| array   | [1,2,3]               | 任意的JS数组           |
| tuple   | [1,2]                 | 元素，定长数组（类似Python） |
| enum    | Enum{A, B}            | 枚举                |

```typescript
联合类型：多个类型（变量可以是多个类型其中一个）
let a: 'male' | 'female'
a = 'male' ✅
a = 'hello'❌

let b: boolean | string
b = true / b = 'a' ✅
b = 1                          ❌

unknown类型与any区别：
当声明any类型并给其他已知类型变量时，不会警告；
当声明unknown类型并给其他已知类型变量时，会警告（判断类型||断言后不会警告）
let a:any = 'hello'
let b:unknown = 'hello'
let c:string
c = a ✅
c = b ❌
if (typeof b === 'string') {
  c = b ✅
}
c = b as string ✅
c = <string>b   ✅

void与never在函数返回值的区别：
void：可以有return，但类型只能是undefined,null,或者只有return;
never：是啥都没有，不需要返回的函数（比如报错函数）
function fn(): void {
    return;
}
function ErrorFn(): never {
    throw new Error('报错了！')
}

对象object的声明：
let a:object ❌ 一般声明对象，只是对对象内部的属性进行类型限制
let a: {name: string} ✅ 此时声明的对象中，只能有name，没有或多写属性都会报错（那么可有可无的属性，怎么办捏！！！）
let a: {name: string, age: number, rname?: string} 在属性后加?表示可有可无的属性(那么我们想加多少加多少属性呢！！)
let a: {name: string, [propName: string]: any} 这样除了name属性必写，后面想加多少随便加
```
