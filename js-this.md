##this

> this指的是当前函数执行的主体（谁执行的函数this就是谁）
> 函数外面的this是window，我们一般都研究函数内部this指向问题
> `this是谁和他在哪定义和在哪执行没有任何关系`

```javascript
    function fn(){
        console.log(this) //-> window
    }
    function sum (){
        fn();
    }
    
    var obj = {
        name : 'obj',
        fn   : function (){
            console.log(this)
        }
    } 
    obj.fn();// -> obj
    var f = obj.fn;
    f();// -> window
```

> 在js非严格模式下（默认模式就是非严格模式）

> 1. 自执行函数中的this一般都是window

```javascript
var obj = {
    fn : (function (){
        console.log(this)
        //this -> window
        return function (){
            console.log(this)//this->obj
        }
    })()
}
obj.fn();
```

> 2. 给元素的某个事件绑定方法，当事件出发执行对应方法的时候，方法中的this一般都是当前操作的元素本身

```javascript
    oBox.onclick = function (){
        //this -> oBox
    }
```

> 3. 还有一种方式能够快速区分this，当方法执行，看一看方法名前面是否有`点`，`点`前面是谁this就是谁，没有this一般都是window

------

> 在JS严格模式下（让JS更加严谨）
> 开启严格模式： 在当前作用域的第一行，加上`"use strict"`，开启严格模式，那么在当前作用域下执行的JS代码都是按照严格模式来处理的
> 在JS严格模式下，如果执行主体不明确this是undefined（非严格模式下this指向window）

```javascript
function fn(){
    console.log(this);
}
fn(); //-> window
widnow.fn();//-> window
```
```javascript
"use strict"
function fn(){
    console.log(this);
}
fn();//-> undefined
window.fn()//-> window
```
```javascript
function fn(){
    console.log(this)
}
document.body.onclick = function (){
    fn();// -> windnow
}
```
```javascript
var num =1,
    obj = {
        num : 2,
        fn : (function (num){ 
            this.num *= 2; 
            num += 2;     
            return function (){
                this.num *= 3;
                num++;  
                console.log(num); 
            }
        })(num)//把全局下的num储存的值赋值给形参
    }
var fn = obj.fn;
fn();  //4
obj.fn(); //5
console.log(num,obj.num); //6 6
```

##作用域销毁的问题

> 1. 全局作用域只有在关闭页面的时候才会销毁
> 2. 函数对应的堆内存在没有变量引用的时候才会销毁，例如：

```javascript
var obj = {}; //开了一个堆内存空间，然后把地址告诉可obj，此时obj就占用了这个堆内存，这个内存就不能销毁了，obj = null; 把地址清掉，这样就没有人再占用这个堆内存，在浏览器空闲时会把其销毁
```

> 3. 一般情况下，函数执行完成，形成的私有作用域就会自己销毁
> 4. 函数执行形成一个私有的作用域，在这个作用域外面有东西占用了作用域里面的内容，那么这个私有作用域就不能被销毁了

```javascript
function fn(){
    return function (){}
}
var f = fn();
```

> 5. 不立即销毁的情况
> 函数执行返回一个引用数据类型的值，但是外面没有变量接收，知识把返回的值使用了一次，这样当前形成的作用域处于不立即销毁状态，浏览器空闲时会把没用的同意销毁

```javascript
function fn(){
    return function (n){}
}
fn()(15);
```


