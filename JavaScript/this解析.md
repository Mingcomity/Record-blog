# this解析

### 1.关于this

**为什么要使用this?**

- this 能提供一种更加优雅的方式来隐式”传递“一个对象引用，再次基础上得以使API的设计更加简洁复用。

  - ```js
    function identify() {
      return this.name.toUpperCase();
    }
    
    function Identify(obj) {
     return obj.name.toUpperCase();
    }
    
    const you = {
      name: 'this'
    }
    
    console.log(identify.call(you))
    
    console.log(Identify(you))
    ```

    > 随着你的使用模式越来越复杂，显示传递上下文对象会让代码变得混乱，使用this可以有效避免这种情况。

**关于this的一些误解：**

- this不指向函数自身
- this也不指向函数的词法作用域

**总结：**

- this是在运行时绑定的，并不是在编写时绑定，它的上下文取决于函数复用时的各种条件。
- this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。

### 2.this绑定判定

> 调用位置：调用位置就是函数在代码中被调用的位置（并非声明的位置）。
>
> 寻找调用位置对理解this是关键。

1. **默认绑定**

   - **独立函数调用**

     > 最常用的函数调用类型，即当函数在不带任何修饰的情况下进行引用调用时应用的方式，此时this指向全局对象。

     ```js
     function foo() {
         console.log(this.a)
     }
     var a = 2;
     foo(); // 2
     ```

     **注意：**在严格模式下，默认绑定将不会绑定在全局对象上，而是绑定在 `undefined` 上。

2. **隐式绑定**

   - **调用位置上是否有上下文对象（是否被某个对象拥有或者包含）**

     > 当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象上。

     ```js
     function foo() {
         console.log(this.a)
     }
     var a = 3;
     let obj = {
         a: 2,
         foo
     }
     foo(); // 3
     obj.foo(); // 2
     ```

     **注意：**被隐式绑定的函数可能会丢失绑定对象，也就是会应用默认绑定，当然得注意是否在严格模式下。

     ```js
     function foo() {
         console.log(this.a)
     }
     var obj = {
         a: 2,
         foo
     }
     var a = 3;
     setTimeout(obj.foo,1000) // 3 or undefined
     ```

3. **显示绑定**

   - **通过call(...)和apply(...)**

     > 两个方法的第一个参数是一个对象，给this准备的，然后在函数调用时将其绑定到this上。

     ```js
     function identify() {
       return this.name.toUpperCase();
     }
     const you = {
       name: 'this'
     }
     console.log(identify.call(you))
     
     ```

   - **硬绑定**

     > bind(...) 函数

     ```js
     function foo(something) {
       return this.a + something;
     }
     const obj = {
       a: 2
     }
     function bind(fn, obj) {
       return function() {
         return fn.apply(obj, arguments)
       }
     }
     const bar = bind(foo,obj);
     console.log(bar(3)) // 5
     ```

4. **new 绑定**

   > 先声明，在Javascript中，new 的机制实际上和面向类的语言不一样。
   >
   > 在Javascript中，**实际上是不存在所谓的 “构造函数”，只有对函数的 “构造调用”。**

   **当使用 new 关键字来调用函数时，会执行如下操作：**

   1. 创建（或者说构造）一个全新对象。
   2. 这个对象会被执行[[Prototype]]连接。
   3. 这个新对象会绑定到函数调用的this。
   4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

   ```js
   function foo(a) {
       this.a = a;
   }
   const bar = foo(2);
   console.log(bar); // 2
   ```

### 3.优先级

比较：

1. 毫无疑问，默认绑定的优先级最低

2. 隐式绑定和显示绑定

   ```js
   function foo() {
     console.log(this.a)
   }
   
   const obj2 = {
     a: 2,
     foo
   }
   
   
   const obj3 = {
     a: 3,
     foo
   }
   
   obj2.foo(); // 2
   obj3.foo(); // 3
   
   obj2.foo.call(obj3); // 3
   obj3.foo.call(obj2); // 2
   
   ```

   **显示绑定优先级更高。**

3. new绑定 和 隐式绑定

   ```js
   function foo(something) {
     this.a = something
   }
   
   const obj1 = {
     foo
   }
   
   
   const obj2 = {
   }
   
   obj1.foo(2) 
   console.log(obj1.a) // 2 
   
   obj1.foo.call(obj2, 3)
   console.log(obj2.a) // 3
   
   const bar = new obj1.foo(4)
   console.log(bar.a) // 4
   console.log(obj1.a) // 2
   
   ```

   **new 绑定 优先级更高。**

4. new 绑定和显示绑定

   > 由于 new 和 call/apply 无法一起使用，所以使用 bind() 硬绑定来测试，注意这也是 显示绑定

   ```js
   function foo(something) {
     this.a = something
   }
   
   const obj1 = {
   }
   
   const bar = foo.bind( obj1 ); // 显示绑定，bar 函数就是显示绑定了obj1
   bar(2);
   
   console.log(obj1.a) // 2
   
   const baz = new bar(3) // 在这之前 baz 的this绑定为 obj1了, 但是 new 调用后，并没有修改原先绑定的 obj1 的a属性，而是在创建了一个baz对象后，把bar函数里的this绑定到了新的对象baz中，所以new确实修改了this的绑定
   console.log(obj1.a) // 2 
   console.log(baz.a) // 3
   ```

   **new 绑定 优先级更高。**

### 4.如何判断this

1. 函数是否在 new 中调用，是的话则this绑定的是新创建的对象。
2. 函数是否通过 call、apply 或者 硬绑定调用？ 是的话，则this绑定的是指定的对象。
3. 函数是否在某个上下文对象中调用，是的话，则绑定的是那个上下文对象。
4. 如果都不是的话，使用默认绑定。但想要注意是否在严格模式下。

**注意：**凡是都有例外

### 5.this绑定的例外情况

- 被忽略的this

  > 出于某种情况(1.使用apply来展开数组传参。2.bind对参数柯里化)将 null、undefined 作为this的绑定对象传入 call、apply、bind ，实际上最后应用的是 **默认绑定**

- 间接引用

  > 你有可能无意或者有意的创建一个函数的“间接引用”，在这种情况下，调用这个函数会应用默认绑定规则