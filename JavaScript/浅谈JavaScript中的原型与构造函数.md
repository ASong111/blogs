### 对象

在JavaScript中对象被定义为一组属性的无序集合，对象的每个属性和方法都由一个名称来标识，这个名称映射到一个值，名称只能是字符串，值可以是一个数据或者函数。如果我们要表示一些事物那么可以用对象来表示：

在下面的例子中，声明了两个对象，具有相同的属性和方法

```js
let person1 = {
    name:"zhangsan",
    age:30,
    sayWord:()=>{
        console.log(`i am zhangsan`);
    }
}

let person2 = {
    name:"lisi",
    age:27,
    sayWord:()=>{
        console.log(`i am lisi`);
    }
}
```

### 工厂模式

如果需要表示1000个人呢？那这种方法就太笨了。可以工厂模式优化一下。工厂模式用于抽象创建特定对象的过程。

```js
/**
 * 工厂模式
 */
let createPerson = function (name,age) {
    let person = new Object();
    person.name = name;
    person.age = age;
    person.sayWord = function () {
        console.log(`i am ${name}`);
    }
    return person;
}

let person1 = createPerson('zhangsan',28);
let person2 = createPerson('lisi',29);

person1.sayWord();
person2.sayWord();

console.log(person1 instanceof Obejct)
console.log(person2 instanceof Obejct)
/**
 * i am zhangsan
 * i am lisi
 * 
 *	true
 *	true
 */
```

这种方式但是解决了多次创建类似对象的问题，但是会有对象类型标识的问题。这样创建出来的对象就只能是Object类型的。为了解决这个问题可以使用构造函数。

### 构造函数

构造函数用于创建特定类型的对象，在JavaScript中像Object和Array是原生的构造函数，可以直接在执行环境中使用，我们也可以自定义构造函数。

```js
/**
 * 构造函数
 */
function Person(name,age) {
    this.name = name
    this.age = age
    this.sayWord = function () {
        console.log(`i am ${name}`);
    }
}

let person1 = new Person('zhangsan',28);
let person2 = new Person('lisi',29);

person1.sayWord();
person2.sayWord();

console.log(person1 instanceof Person)
console.log(person2 instanceof Person)
/**
 * i am zhangsan
 * i am lisi
 * 
 *	true
 *	true
 */
```

以上代码创建了一个名为Person的构造函数，注意构造函数的命名需要首字母大写，这是在其他的面向对象语言中借鉴的，用于区别普通函数。使用构造函数虽然可以解决对象类型标识的问题，但是也存在一些问题，那就是构造函数中定义的方法，会随着对象的实例化每次都创建一遍。

```js
console.log(person1.sayWord === person2.sayWord );
/**
* false
*/
```

这个sayWord函数做的事是一样的，所以没必要创建多次。要解决这个问题可以使用JavaScript中的原型特性。

### 原型

每个函数都会自动创建一个prototype的属性，这个属性是一个对象，它包含了特定引用类型(例子中的Person)的实例(person1,person2)共享的属性和方法。实际上，这个对象就是通过构造函数创建的对象的原型。使用原型的好处就是在原型对象上面创建的属性与方法可以被每个实例对象共享。这样就可以避免sayWord函数被多次创建。

```js
function Person(name,age) {
    this.name = name
    this.age = age
}

Person.prototype.sayWord = function () {
    console.log(`i am ${this.name}`);
}

let person1 = new Person('zhangsan',28);
let person2 = new Person('lisi',29);

person1.sayWord();
person2.sayWord();
console.log(person1.sayWord === person2.sayWord );

/**
 * i am zhangsan
 * i am lisi
 * true
 */
```

所有的原型对象都会自动获得一个名为constructor的属性，指回与之关联的构造函数。

```js
console.log(Person.prototype.constructor);
/**
 * [Function: Person]
 */
```

### 原型链

对于函数可以使用prototype属性获得原型对象，那么对象能不能知道自己的原型对象是什么呢？

当然可以。在JavaScript中统一的做法就是使用Object.getPrototypeOf。也有其他的方式可以访问，在 Firefox、Safari 和 Chrome 会在每个对象上暴露\__proto__属性

```js
//实例对象person1
console.log(Object.getPrototypeOf(person1));
//普通对象 {}
console.log(Object.getPrototypeOf(new Object()));
/**
 * { sayWord: [Function (anonymous)] }
 * [Object: null prototype] {}
 */
```

原型对象也是对象，也可以有自己的原型，逐渐构成了**原型链**。原型链终止于拥有 `null` 作为其原型的对象上。

当你试图访问一个对象的属性时，如果在对象本身的属性中没有找到，那么就会去对象的原型中找。如果仍然找不到该属性，就回去原型的原型中找，以此类推。直到找到该属性，或者到达链的末端，此时会返回undefined。

下图很好的展示了原型链

![img](https://asong-1319116310.cos-website.ap-guangzhou.myqcloud.com/asong/202307160146539.png)

### 继承

继承是面向对象编程中独有的概念，它想让程序员表达这样的想法：在系统中，一些对象是另一个对象更具体的版本。例如：在之前的例子中我们创建了一个名为Person的构造函数，并且实例化了两个对象。这两个实例对象除了身份是人之外，还可以有更具体的身份，比如：教师，学生。他们都可以继承人的属性和方法。

```js
function Person() {
}

Person.prototype.bone = 206;

Person.prototype.sayWord = function () {
    console.log(`i am a person`);
}

Person.prototype.run = function () {
    console.log(`running`);
};

function Teacher(name,age,course) {
    this.name = name;
    this.age = age;
    this.course = course;
}
Teacher.prototype = new Person();

Teacher.prototype.sayWord = function () {
    console.log(`i am ${this.course} teacher ${this.name} `);
}

let t = new Teacher("zhangsan",50,"math");
t.sayWord();
t.run();
console.log(t.bone);

/**
 * i am math teacher zhangsan
 * running
 * 206
 */
```

JavaScript中实现继承的方式除了原型链还有很多，我认为这些方式不需要过多去了解，es6中的class关键字已经统一了规范。

### 参考

https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes

JavaScript高级程序设计第4版

https://www.zhihu.com/question/34183746

