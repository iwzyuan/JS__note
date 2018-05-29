#作用域（scope）

> [栈内存]
> 全局作用域：window
> 私有作用域： 函数执行
> 块儿级作用域：使用let创建变量存在块儿级作用域
> 
> [作用域链]
> 当前作用域执行遇到一个变量，我们首先看一下它是否属于私有变量，如果是当前作用域私有变量，那么以后在私有作用域中在遇到这个变量都是操作私有（`闭包：私有作用域保护私有变量，防止外界干扰`）：如果不是私有变量，向其上级作用域查找，也不是上级作用域私有的继续向上查找，一直到window全局作用域为止，我们把这种机制，称为`作用域链`，全局下有操作的就是全局的，全局下没有（设置：给全局对象增加了属性名。获取的话会报错）

###查找私有变量

> JS中的私有变量有且只有两种

> - 在私有作用域变量提升阶段，声明过的变量或者函数
> - 形参也是私有变量

```javascript
function fn(num1,num2){
    var total = num1 + num2;
    return total;
}
var result = fn(100,200);
```

> 函数执行形成了一个新的作用域
> 1. 形参赋值
> 2. 变量提升
> 3. 代码自上而下执行
> 4. 当前栈内存（私有作用域）销毁或者不销毁

```javascript
var x = 10,
    y = 20,
    z = 30;
function fn(x,y){
    //=> [私有作用域]
    //=> 形参赋值：x=10,y=20 (x和y都是私有变量)
    //=> 变量提升： var x （忽略，已经存在x这个名字）
    console.log(x,y,z);//10 20 30
    var x = 100;//私有的x变为100
    y = 200;//私有的变为200
    z = 300;//全局变为300
    console.log(x,y,z);//100 200 300
}
fn(x,y,z);
console.log(x,y,z);//10 20 300

function fn(b){
    //=> [私有作用域]
    //=> 形参赋值 ： b = 1（私有变量）
    //=> 变量提升： b = aaafff111;
    console.log(b); //b函数本身
    function b(){
        console.log(b);
    }
    b(); //函数本身
}
fn(1);
```

###如何查找上级作用域

> 函数执行形成一个私有作用域（A），A的上级作用域是谁，和他在哪儿执行没关系，主要看他是在哪定义的，在哪个作用域下定义的，当前A的上级作用域就是谁

```javascript
    var n = 10;
    function sum(){
        console.log(n);
    }
    sum();//=> 10
    
    ;function (){
        var n = 100;
        sum(); //=> 10
    }()
```

**闭包-选项卡**
```javascript
//自定义属性
for(var i=0; i<oList.length; i++){
    oList[i].index = i;
    oList[i].onclick = function (){
        changeTab(i)
    }
}
//闭包机制保存变量实现
//1. 
for(var i=0; i<oList.length; i++){
    oList[i].onclick = (function (i){
    //闭包形成不销毁的作用域，因为return的值被接收了
    //return 小函数定义时上级作用域为立即执行函数，能接收到保存的变量i
    return function (){
        changeTab(i);
    }
    })(i)
}
//2. 
for(var i=0; i<oList.length; i++){
    ~function (i){
        oList[i].onclick = function (){
            changeTab(i);
        }
    }(i)
    oList[i].onclick = (function (i){
    //闭包形成不销毁的作用域，因为return的值被接收了
    //return 小函数定义时上级作用域为立即执行函数，能接收到保存的变量i
    return function (){
        changeTab(i);
    }
    })(i)
}
//3.
//用ES6块儿级作用域
for(let i=0; i<oList.length; i++){
    oList[i].onclick = function (){
        changeTab(i)
    }
}
```  