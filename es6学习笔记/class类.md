## class

#### 三个特征：封闭、继承、多态

**封闭 **理解为整个代码块是封闭的

```javascript
class Father{
    constructor(name,age){
        this.name = name
        this.age = age
        // this.show() 调用show方法
    }
    show(){
        return `name:${this.name},age:${this.age}`
    }
    money(){
        return 100
    }
}

const bob = new Father('ww',36)
```

一般声明类 默认首字母大写，在es6中类没有变量提升，只有先定义了类，才能实例化对象。

类里面的共有的属性和方法的使用一定要加this

new操作符实例化对象的时候，自动执行constructor函数里面的代码

```javascript
class Son extends Father{
    constructor(x,y){
        super(x,y) // 使用子类里面的this 必须先调用super（强制要求）
        this.x = x
        this.y = y
    }
    show(){
        // super.show() 调用父类里面的方法
        return `爸爸的儿子的name:${this.name},age:${this.age}`
    }
}

const son = new Son('ww',18)
son.show() // 爸爸的儿子的name:ww,age:18
```

关键字extends可以让子类继承父类的属性和方法

关键字super可以调用父类的构造函数和普通函数

继承中的属性或者方法查找原则：**就近原则**

#### 类中的this指向问题

constructor里面的this指向实例化对象

类里面的方法，谁调用方法，方法里面的this就指向谁

