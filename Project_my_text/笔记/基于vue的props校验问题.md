
源码解析
declare interface PropOptions<T = any, D = T> {
    type?: PropType<T> | true | null;
    required?: boolean;
    default?: D | DefaultFactory<D> | null | undefined | object;
    validator?(value: unknown): boolean;
}

export declare type PropType<T> = PropConstructor<T> | PropConstructor<T>[];

declare type PropConstructor<T = any> = {
    new (...args: any[]): T & {};
} | {
    (): T;
} | PropMethod<T>;

declare type PropMethod<T, TConstructor = any> = [T] extends [
((...args: any) => any) | undefined
] ? {
    new (): TConstructor;
    (): T;
    readonly prototype: TConstructor;
} : never;


思路：我们需要的是一个无参函数的返回，才能通过校验
https://www.404bugs.com/index.php/details/1087849346920927232