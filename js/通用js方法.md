###### 对比两个对象的值是否完全相等 返回值true/false

```javascript
isObjectValueEqual (a, b) {   
      //取对象a和b的属性名
      var aProps = Object.getOwnPropertyNames(a);
      var bProps = Object.getOwnPropertyNames(b);
      //判断属性名的length是否一致
      if (aProps.length != bProps.length) {
          return false;
      }
      //循环取出属性名，再判断属性值是否一致
      for (var i = 0; i < aProps.length; i++) {
        var propName = aProps[i];
        if (a[propName] !== b[propName]) {
            return false;
        }
      }
      return true;
    },
```

验证以上的方法

```javascript
var obj1 = { name: "Benjamin", sex : "male"};

var obj2 = { name: "Benjamin", sex : "male"};

var obj3 = obj1;//obj1和ob3的指针指向了内存中的同一个地址

console.log(isObjectValueEqual(obj1, obj2));//true
 
console.log(obj1 == obj3);//Outputs: true
 
console.log(obj1 === obj3);//Outputs: true
 
console.log(obj2 == obj3);//Outputs: false
 
console.log(obj2 === obj3);//Outputs: false
```