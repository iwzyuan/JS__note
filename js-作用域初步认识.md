##作用域链初步认识
```javascript
console.log(a); //undefined  变量提升，声明了a变量
console.log(window.a);//undefined
var a = 12;
console.log(a); //12
console.log(window.a); //12
console.log(b); //Uncaught ReferenceError: b is not defined 不带var并没有声明变量而是给window添加了b的属性
b = 13;
```
###定义变量的时候带var和不带var的区别

> [带var]
> 在当前作用域中声明了一个变量，如果当前是全局作用域，也相当于给全局作用域设置了一个属性
> [不带var]
> 在全局作用域中如果不带var，仅仅是给全局对象设置了一个新的属性名

###作用域链

> 1. 函数执行形成一个私有作用域（保护私有变量），进入到私有作用域当中，首先变量提升（声明过的变量是私有的），接下来代码执行：
> 遇到一个变量如果这个变量是私有的，那么按照私有处理即可
```javascript
function fn(){
//=> a为当作用域内的私有变量
    console.log(a); //变量提升
    var a  = 12;
    console.log(a); //变量定义
}
fn();
console.log(a); //报错
```
> 2. 如果当前这个变量不是私有的，我们需要向他的上级作用域进行查找，上级如果也没有，则继续向上查找，一直找到window全局作用域为止，我们把这种查找机制叫做 **作用域链**
> 1) 如果上级作用域有这个变量，我们当前操作的都是上级作用域中的变量（加入我们在当前作用域把值改了，相当于把上级作用域中的这个值修改了）
> 2) 如果上级作用域中没有这个变量
> - 变量 = 值：相当于给window设置了一个属性

```javascript
var a = 12;
function fn(){
    console.log(a); //12 => 用的是全局变量a的值
    a = 13; //根据作用域链，使用的是上一级的a
    console.log(a);//13
}
fn();

var a = 12;
function fn(){
    console.log(a); //undefined  私有变量存在var，进行了变量提升
    var a = 13; //私有作用域声明定义了a
    console.log(a);//13
}
fn();

function fn(){
    a = 13; //未找到a，上一级乃至window都没有，会将他定义为window的属性a
    console.log(a);//13
}
fn();
console.log(a) //13 window.a的值

console.log(x+','+ y); //undefined  undefined
var x = 10,  //等同于var a = 10;var y = 12;
    y = 12;
function fn(){
    // => [私有作用域]
    // 变量提升
    console.log(x+','+ y); //undefined  12
    var x = y = 100; //等同于var x = 100（私有）; y = 100(全局);
    console.log(x+','+ y); //100 100
}
fn();
console.log(x+','+ y); //10 100
```

###只对等号左边的进行变量提升

> =：赋值，左边是变量，右边都应该是值

```javascript
//之前
i%2 === 0 ? item.className = 'c1' : item.className = 'c2'
//现在
item.className = i%2 ? 'c1':'c2'
```

```javascript
//函数表达式
sum(); //typeError
var sum = function (){
};

//声明函数
sum1(); //可以调用到
function sum1(){
}
```

> 真实项目中，应用这个原理，我们创建函数的时候可以使用函数表达式的方式：
> 1. 因为只能对等号左边进行提升，所以变量提升后，当前函数只是声明了，没有定义，想要执行函数，`只能在赋值的代码之后执行`（放在前面执行相当于让undefined执行，会报错的）
> 2. 这样让我们的代码逻辑更加严谨，以后想要知道一个执行的函数做了什么功能，只需要同上查找定义的部分即可（不会在定义）的代码在执行下面的情况

```javascript
var fn = function sum(){
        console.log(sum);/*ƒ sum(){  在函数私有作用域内可以打印出函数本身
            console.log(sum);
            console.log(1);
        }*/
        console.log(1);
    }
    //sum();//ReferenceError: sum is not defined
```

###不管条件是否成立都要进行变量提升

> 不管条件是否成立，判断题当中出现的var或者function都会进行变量提升，但是在新版本浏览器中function声明的变量只能提前声明不能定义了（前提：函数在判断体中）

```javascript
console.log(fn);//=>undefined
if(true){
    var fn = 13;
    function fn(){
    }
}
```

> 代码执行到条件判断地方
> [条件不成立]
> 进入不到判断体中，此时之前声明的变量或者函数依然是undefined
> 
> [条件成立]
> 进入条件判断体中的第一件事情不是代码执行，而是把之前变量提升没有定义的函数首先定义了（进入到判断题中函数就定义了：为了迎合ES6的块级作用域）

```javascript
console.log(fn); //=> undefined
if(true){
    console.log(fn); //=>函数体本身
    function fn(){
    }
    console.log(fn);//=> 函数体本身
}
```

##JS重名的处理

> 在变量提升阶段，如果名字重复了，不会重新的进行声明，但是会重新的进行定义（后面赋的值会把之前的值替换）

```javascript
    //=> 变量提升：fn = aaafff111 => aaafff222=> aaafff333 => aaafff444
    fn(); // 4
    function fn(){console.log(1);}
    fn(); //4
    function fn(){console.log(2);}
    var fn = 13; // => fn = 13
    fn(); //=> 13(); Uncaught TypeError:fn is not a ...,下面不走了
    function fn(){console.log(3);}
    fn();
    function fn(){console.log(4);}
    fn();
```




