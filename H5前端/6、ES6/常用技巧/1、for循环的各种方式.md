1. **for**

 循环：for 循环是最常用的循环方式，在循环开始前定义一个计数器变量，循环条件使用计数器变量，并且每次循环结束后更新计数器变量。通常用于已知循环次数的情况。

```
javascriptCopy Codefor (let i = 0; i < 10; i++) {
    console.log(i);
}
```

1. **forEach**

 循环：forEach 循环是一种专门用于遍历数组的循环方式，它可以遍历数组中的每个元素，并对其进行操作。需要注意的是，forEach 循环不支持 break 和 continue 语句。

```
javascriptCopy Codeconst arr = [1, 2, 3];
arr.forEach((item, index) => {
    console.log(`第${index}个元素是${item}`);
});
```

1. **for...of**

 循环：for...of 循环用于遍历可迭代对象（如数组、字符串、Map、Set等），可以直接访问每一个元素的值。与 

```
javascriptCopy Codeconst arr = [1, 2, 3];
for (let item of arr) {
    console.log(item);
}
```

1. **for...in**

 循环：for...in 循环用于遍历对象的可枚举属性（包括自身属性和继承属性）。通过该循环，可以获取对象的键名（属性名）。

```
javascriptCopy Codeconst obj = {
    name: 'Alice',
    age: 25,
    gender: 'female'
};
for (let key in obj) {
    console.log(key);
}
```