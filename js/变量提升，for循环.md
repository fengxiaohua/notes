# 变量提升与for循环
let在for循环内每次会声明赋值一次，与作用域块有关
```
for(var i; i = 0; i++) {
  setTimeout(function(){console.log(i)},0)
}

for(let i; i = 0; i++) {
  setTimeout(function(){console.log(i)},0)
}
```