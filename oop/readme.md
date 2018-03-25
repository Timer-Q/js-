## 创建对象的方式

1. 工厂模式
```javascript
    function createPerson(name, age, job) {
        var o = new Object();
        o.name = name;
        o.age = age;
        o.job = job;
        return o;
    }
    const persion1 = createPerson('niuniu', 18, 'developer')
    const persion2 = createPerson('huahua', 16, 'dreamer')
```
> 工厂模式虽然解决了创建多个相似对象的问题，但去没有解决对象识别的问题（既怎样知道一个对象的类型）

2. 构造函数模式
```javascript
    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = function() {
            console.log(this.name);
        };
    }
    const persion1 = new Person('niuniu', 18, 'developer')
    const persion2 = new Person('huahua', 16, 'dreamer')
```
构造函数执行4个步骤：   
（1）创建一个新对象；  
（2）将构造函数的作用域赋给新对象（因此，this 就指向了这个新对象）；   
（3）执行构造函数中的代码（为这个新对象添加属性）；   
（4）返回新对象

person1 和 person2 分别保存着Person的一个不同的实例。这两个对象都有一个constructor属性，该属性指向Person
```javascript
    person1.constructor === Person // true
    person2.constructor === Person // true
```
> 通过 instanceof 可以知道，创建的实例既是 Object 的实例，也是 Person 的实例；   
创建自定义个构造函数意味着将来可以将它的实例标识为一种特定的类型；

***   

> 缺点：   
每个方法都要在每个实例上重新创建一遍，例如 sayName()
```javascript
    person1.sayName === person2.sayName // false
```
> 可以把 sayName 定义到构造函数外面；
```javascript
    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.satName = sayName;
    }
    function sayName() {
        console.log(this.name)
    }
```
> 缺点：  
如果对象需要定义很多方法，就会定义很多全局对象；   
而全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不副实。

3. 原型模式

> 我们创建的每个函数都有一个prototype属性，这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法；   
使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法
```javascript
    function Person() {}
    Person.prototype.name = 'niuniu';
    Person.prototype.age = 18;
    Person.prototype.job = 'developer';
    Person.prototype.sayName = function() {
        console.log(this.name);
    }
    var person1 = new Person();
    person1.sayName(); // 'niuniu'
    var person2 = new Person();
    person2.sayName(); // 'niuniu'
    person1.sayName === person2.sayName // true
```
> 无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数常见一个 prototype 属性，这个属性指向函数的原型对象。在默认情况下，所有原型对象都会自动获得一个 constructor 属性，这个属性包含一个指向 prototype 属性所在函数的指针。通过这个构造函数，我们还可以为原型对象添加其他属性和方法。   
***
创建了自定义的构造函数之后，其原型对象只会取得 constructor 属性；至于其他方法，则都是从Object继承而来的。  
***
当调用构造函数创建一个新实例后，该实例的内部将包含一个指针，指向构造函数的原型对象；  
***这个连接存在于实例与构造函数的原型对象之间，而不是实例与构造函数之间***

<img src="./images/createObject-prototype.png" width="600"/>

> isPrototypeOf() 方法来确定对象之间是否存在这种关系
```javascript
    Person.prototype.isPrototypeOf(person1) // true
    Person.prototype.isPrototypeOf(person2) // true
```
> getPrototypeOf() 返回 __proto__ 的值

```javascript
    Object.getPrototypeOf(person1) === Person.prototype // true
```
<img src="./images/createObject-prototype2.png" width="600"/>

4. 组合使用构造函数模式和原型模式
> 结合构造函数和原型模式的有点  
构造函数可以传值   
原型可以定义公共方法
```javascript
    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
    }
    Person.prototype.sayName = function() {
        console.log(this.name)
    }
```
5. 动态原型模式
6. 寄生构造函数模式
7. 稳妥构造函数模式

## 继承
> oo语言有两种继承方式：接口继承(只继承方法签名)、实现继承(继承实际的方法)

1. 原型链：利用原型让一个引用类型继承另一个引用类型的属性和方法
```javascript
function SuperType () {
    this.property = true;
}
SuperType.prototype.getSuperValue = function () {
    return this.property;
}
function SubType() {
    this.subProperty = false;
}
SubType.prototype = new SuperType()
SubType.prototype.getSubValue = function () {
    return this.subProperty;
}
var instance = new SubType()
instance.getSuperValue() // true
```
<img src="./images/extends-prototype.png" width="600"/>

> 此处有个地方需要注意：
instance.getSuperValue() 的返回值为true，是因为 SubType.prototype 是 SuperType 的实例，SubType.prototype 上也就有了 property 这个实例属性，然后 instance 调用 getSuperValue 方法的时候，发现 instance 上没有 property 这个属性，然后沿着原型链往上找，在 SubType.prototype 上找到了。  

> 原型链继承无法避免的问题:   
如果原型上有引用类型，那么其对应的所有实例将共享该引用类型，其中一个实例改变了该值，其他实例取到的值也将发生变化。   
构建子类实例的时候，不能向超类构造函数中传值；实际上，应该说是没有办法在不影响所有对象实例的情况下，给超类的构造函数传递参数

2. 借用构造函数
> 在子类型构造函数中调用超类型构造函数
```javascript
    function SuperType() {
        this.colors = ['red', 'green', 'blue'];
    }
    function SubType() {
        SuperType.call(this);
    }
    var instance1 = new SubType()
    instance1.colors.push('black')
    console.log(instance1.colors) // red, green, blue, black
    var instance2 = new SubType()
    console.log(instance2.colors) // red, green, blue
```
优势：
传递参数
相对于原型链而言，借用构造函数的一大优点是可以在子类型构造函数中向超类构造函数中传参。
```javascript
    function SuperType(name) {
        this.name = name
    }
    function SubType(name, age) {
        SuperType.call(this, name)
        this.age = age
    }
    var instance = new SubType('niuniu', 18)
    console.log(instance.name, instance.age) // => niuniu 18
```
劣势：
公共方法无法实现继承

3. 组合式继承（伪经典继承）
原型链实现对原型属性和方法的继承，构造函数实现实例属性的继承。这样，即通过在原型上定义方法，实现了方法复用，又能保证每个实例有自己的属性
```javascript
    function SuperType(name) {
        this.name = name
        this.colors = ['red', 'green', 'blue']
    }
    SuperType.prototype.sayName = function() {
        console.log(this.name || 'you do not have name')
    }
    function SubType(name, age) {
        SuperType.call(this, name)
        this.age = age
    }
    SubType.prototype = new SuperType()
    SubType.prototype.constructor = SubType
    SubType.prototype.sayAge = function() {
        console.log(this.age)
    }
    var instance1 = new SubType('niuniu', 18)
    instance1.colors.push('black')
    instance1.sayName()
    instance1.sayAge()
    console.log(instance1.colors)

    var instance2 = new SubType('huahua', 17)
    instance2.sayName()
    instance2.sayAge()
    console.log(instance2.colors)
```
> 注： 由于 SubType.prototype 是 SuperType 的实例，故 SubType.prototype 中存在实例属性 name、colors   
instaceof isPrototypeOf() 也能用于基于组合继承的对象

4. 原型式继承
原型式继承有个典型代表：Object.create()

虽然不知道具体逻辑的实现，简单原理如下：
```javascript
    function object(o) {
        function F() {}
        F.prototype = o
        return new F()
    }
```
Object.create() 接收两个参数，如果只传一个的话，就如上面所写那样；   
第二个参数与 Object.definedProperties() 方法的第二个参数相似，每个属性都是通过自己的描述定义的，
以这种方式定义的任何属性，都会覆盖原型上的同名属性

```javascript
    var person = {
        name: 'niuniu',
        friends: ['huahua']
    }
    var anotherPerson = Object.create(person, {
        name: {
            value: 'Tony' // 该值是挂在在anotherPerson下面
        }
    })
    console.log(anotherPerson.name) // Tony
```

5. 寄生式继承
类似工厂模式
```javascript
    function createAnother(original) {
        var clone = object(original)
        clone.sayHi = function() {
            console.log('hi')
        }
        return clone
    }
    var person = {
        name: 'niuniu',
        friends: ['huahua']
    }
    var anotherPerson = createAnother(person)
    anotherPerson.sayHi() // => hi
```
6. 寄生组合式继承

组合继承最大的问题：无论在什么情况下，都会调用两次超类构造函数；一次是在创建子类型实例的时候，一次是在子类型构造函数中。   
第一次调用的时候，会在 SubType.prototype 上创建多余的实例属性。   

为了提高效率，解决以上问题，故而使用寄生组合式继承。   

寄生组合式继承主要思想：
> 使用寄生的方式，使 Subtype.prototype 与 SuperType.prototype 产生继承关系，然后利用组合继承的方式，继承属性。
```javascript
    function inheritPrototype(subType, superType) {
        // var prototype = object(superType.prototype)
        var prototype = Object.create(superType.prototype) // 创建对象
        prototype.constructor = subType // 增强对象
        subType.prototype = prototype // 指定对象
    }
    function SuperType(name){
        this.name = name;
        this.colors = ["red", "blue", "green"];
    }
    SuperType.prototype.sayName = function(){
        alert(this.name);
    };
    function SubType(name, age){
        SuperType.call(this, name);
        this.age = age;
    }
    inheritPrototype(SubType, SuperType);
    SubType.prototype.sayAge = function(){
        alert(this.age);
    };
```
