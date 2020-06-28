# 一、ES6

## Array

### 1. reduce()

参考:https://blog.csdn.net/qq_36711388/article/details/89854535

#### 描述

```javascript
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)

/*
参数1(必需):function(total, currentValue, currentIndex, arr)
	执行每个数组元素的函数。
	-- total 					必需 计算结束后的返回值,初始值 = initialValue
	-- currentValue		必需 当前元素
	-- currentIndex		可选 当前元素索引
	-- arr						可选 当前元素所属对象
	
参数2(可选):initialValue,默认0
	传递给函数的初始值
	
返回值 累计的total。
*/
```



#### 应用场景

##### 	(1) 计算总和

```javascript
var total = [ 2, 3, 4 ].reduce(( pre, next ) => {
    return pre + next
});

console.log(total)   // 9
```

##### 	(2) 简单数组去重

```javascript
let arr = [1,2,3,4,5,2,3]
let result = arr.reduce((prev,cur)=>{
     if (!prev.includes(cur)){
        prev.push(cur)
     }
    /* 避免 includes 的性能影响
     if (prev.length === 0 || init[prev.length - 1] !== cur) {
          prev.push(current);
      }
    */
  
     return prev
},[])
console.log(result)
```

##### 	(3) 二维数组转为一维数组

```javascript
var array = [[1, 2], [3, 4], [5, 6]].reduce((  pre, next ) => {
    return pre.concat(next)
}, []);

console.log(array)  // [1, 2, 3, 4, 5, 6]
```

##### 	(4) 计算数组中每个元素出现的次数

```javascript
let names = ['tom', 'jim', 'jack', 'tom', 'jack'];
const countNames = names.reduce((allNames, name) => {
  if (name in allNames) {
    allNames[name] ++;
  } else {
    allNames[name] = 1;
  }
  return allNames;
}, {});
console.log(countNames)  // { tom: 2, jim: 1, jack: 2 }


二、
const arraySum = (arr, val) => arr.reduce((acc, cur) => {
    return cur == val ? acc + 1 : acc + 0
}, 0);
let arr = [ 0, 1, 3, 0, 2, 0, 2, 3 ]

```



## Object

### 1. assign()

#### 描述

```javascript
Object.assign(target, source1, source2,...,sourceN);

/*
参数： source1, source2...sourceN 到 target,
返回 target
*/
```

# 二、ES7

