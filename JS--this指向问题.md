# JS--this指向问题

### 1.function中的this在不同环境下的指向
- #### 事件调用环境：
  谁触发的事件，函数里面的this就指向谁
- #### 全局环境下：
  在浏览器的环境中，this指向window；在node环境下，this指向module.exports
- #### 函数内部：
  - ##### [this最终指向的都是调用他的对象]
    指向的也是window，声明一个变量，一个函数，其实都是在window中声明     一个对象，所以this都是指向window。
  - ##### [函数被多层对象所包含，如果函数被最外层对象调用，this指向的只是它上一级对象]
  - ##### [在构造函数中this指向的是实例对象]
    ```
        <scrpit>
            function Fn(){
            this.num = 10
            console.log(this) //Fn{num:10}
            }
            
            var obj = new Fn()
            
            // new Fn()具体做了那些事？
            // 1.调用该构造函数
            // 2.在该构造函数内部创建一个对象
            // 3.将构造函数的this与该对象绑定
            // 4.如果该构造函数没有返回值，将return步骤2中创建的对象(this指向的对象)
        </script>
    ```

    ```
        <scrpit>
            function Fn(){
            this.num = 10
            }
            
            var num = 20
            Fn.prototype.num = 30
            Fn.prototype.method = function(){
            console.log(this.num)
            }
            var prototype = Fn.prototype
            var method = prototype.method
            
            //当调用某种方法或查找某种属性时，首先会在自身调用和查找，如果自身并没有该属性或方法，则会去它的__proto__属性中调用查找，也就是它构造函数的prototype中调用查找
            new Fn().method() //输出10
            method()          //输出20
            prototype.method()//输出30
        </script>
            
    ```
  - ##### [如果构造函数中有return，且return的值是对象，this则指向返回对象，如果不是则指向实例对象，return的值是null还是指向实例对象]

### 2.ES6中箭头函数的this指向

- #### 箭头函数本身是没有this和arguments的，在箭头函数中引用this实际上调用的是定义在上一层作用域中的this，这里强调一下是上一层作用域，因为对象是不能形成独立的作用域的。

    ```
        //不使用箭头函数实现this指向改变
        <script>
            function move(){
                var _this = this //保存当前this
                setTimeout(function(){
                    _this.style.left = "100px"
                },1000)
            }
        </script>
    ```
    ```
        //使用箭头函数
        <script>
            function move(){
                setTimeout(() => {
                    this.style.left = "100px"
                },1000)
            }
        </script>

    ```
- #### 这里强调一下，对象是不能形成独立的作用域的。
    ```
        <script>
            var obj = {
                fn : () => {
                    console.log(this)
                }
            }
            
            obj.fn() // 输出的是window
        </script>
    ```
### 3.修改函数中this的指向
- #### 通过call()、apply()、bind()方法，可以修改this指向,三个方法主要都是用于修改this指向问题，但是在传参的时候有一些差别:
   - call()方法传参  
    ```
        <script>
            var box = document.querySelector('.box')
            var a,b,c
            var obj = {
                fn : function(){
                    console.log(this)
                }
            }
            //通过call()方法，可以修改this指向
            obj.fn.call(box,a,b,c)
        </script>
    ```
    - apply()方法传参  
    ```
        <script>
            var box = document.querySelector('.box')
            var a,b,c
            var obj = {
                fn : function(){
                    console.log(this)
                }
            }
            //通过apply()方法，可以修改this指向
            obj.fn.apply(box,[a,b,c])
        </script>
    ```
    - bind()方法传参,bind()并不会返回this指向，只是修改this指向,需要再次调用才会返回this指向
    ```
        <script>
            var box = document.querySelector('.box')
            var a,b,c
            var obj = {
                fn : function(){
                    console.log(this)
                }
            }
            //通过bind()方法，可以修改this指向
            obj.fn.bind(box,a,b,c)()
        </script>
    ```
- #### ES6中箭头函数函数this指向在创建时就已经定义好了，无法修改。