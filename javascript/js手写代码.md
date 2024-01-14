## css驼峰格式
```
function cssStyle2DomStyle(sName) {
    var arr=sName.split('-');
    for(var i=0;i<arr.length;i++){
        if(arr[0]==""){
            arr.shift();
            // 但是无论如何，第一个都不会大写，因此下一步直接判断下下个
            continue;
        }
        if(arr[i]&&i!=0){
            arr[i]=arr[i].slice(0,1).toUpperCase()+arr[i].slice(1)
        }
    }
    return arr.join('')
}
```
* 需要注意toUpperCase是对字符串整体都变大写，要实现首字母大写需要切割为只剩一个字母

## 生成圆形的点击区域
1. `虽然使用border-radius后的元素占据宽高还是原来那么多，但是实际有效区域改变了`
```
.one{
	background: #0000FF;
	width: 100px;
	height: 100px;
	border-radius: 50%;
}
<div class="one" onclick="change()">
</div>
function change(){
	console.log('1432')
}
```
* `使用border-radius后，原来点击生效的区域不生效了`
2. 使用原生js+canvas来绘制一个圆形，然后使用勾股定理确定位置在不在范围内
```
<canvas id="one" width="300" height="300"></canvas>

// 获取画布canvas
var canvas=document.getElementById('one').getContext('2d');
var radius=50;
canvas.fillStyle='#00f'
canvas.arc(150,150,radius,0,2*Math.PI,false)
canvas.fill();//必须使用fill方法填充才有颜色

document.onmousemove=function(e){
	var e=e?e:window.event;
	// 根据鼠标的x,y轴位置判断是否在元素上
	var x=Math.abs(150-e.clientX);
	var y=Math.abs(150-e.clientY);
	// 使用勾股定理
	if(Math.sqrt(Math.pow(x,2)+Math.pow(y,2))<radius){
		console.log('323')
	}
}
```

## 形参的几种形式
```
			    // 1. Arguments(2) ["a", 11, callee: ƒ, Symbol(Symbol.iterator): ƒ]
				console.log(arguments);
				// 2. a 11 形参的字符串表示 a 11
				console.log(...arguments);
				// 3. 数组表示形式 ["a", 11] 
				console.log([...arguments])
```

## 手写bind
```
			// 手写bind 
			Function.prototype.bind=function(context,...bindments){
				// 绑定调用该函数的对象
				context=context||window;//{name: "yiyi", age: 11}
				// 绑定好this 使用闭包，到时候返回原来的this
				const func=this;// 被调用的函数!
				
				// 如果不是函数使用bind则提示错误
				if(!(func instanceof Function)){
					console.log('bind方法必须是函数使用')
				}
				return function F(...callArgs){
					let allArgs=bindments.concat(callArgs);// concat返回数组
					// 如果此时是创建实例，new func 而不是调用方法，那么返回实例
					if(this instanceof F){
						console.log("new一个实例")
						return new func(...allArgs)
					}
					// 注意：返回的是对数组展开之后的结果 字符串。。
					return func.call(context,...allArgs)
				}
			}
```
* bind绑定的优先级低于new，所以先bind再new也可以，但是绑定的this会失去
```
			// 1. 直接使用bind方式
			function one(a,b){
				console.log(this.name);
				console.log(a)
				console.log(b)
			}
			var obj={name:'yiyi',age:11}
			var two=one.bind(obj,'a')
			console.log(two);// 回调函数
			two(11);// 执行one函数内容
			
			// 2. 构造函数形式(但是会失去bind(obj,arguments)的obj,arguments依旧有效)
			function three(a,b){
				this.a=a
				this.b=b
				console.log(this)
			}
			var bindf=three.bind(obj,'b');
			bindf(1);// {name: "yiyi", age: 11, a: "b", b: 1}
			// new绑定this的优先级高于bind绑定,所以之前this绑定的对象无效了
			var four=new bindf(33); // 但是之前bind传的值还有效
			console.log(four);// {a: "b", b: 33}
```
* 简洁版
```
			// 手写bind 
			Function.prototype.bind=function(context,...bindments){
				context=context||window;
				const func=this;
				
				return function F(...callments){
					let args=bindments.concat(callments);
					if(this instanceof F){
						return new func(...args);
					}
					return func.call(context,...args)
				}
			}
			
			function one(a,b){
				this.a=a;
				this.b=b;
				console.log(this)
			}
			var obj={name:'yiyi'}
			var child=one.bind(obj,2);
			child(3);
			
			var two=new child(4);
			console.log(two)
```
* `call/apply的实现其实都是利用闭包，把this用一个变量保存起来，方便闭包调用的时候不释放，并且指向这个变量`
## 手写call
```
			// 手写call 
			Function.prototype.call=function(context,...callargs){
				if(!(this instanceof Function)){
                    throw new TypeError("必须是函数调用call");
                }
                context=context||window;
				// 添加属性，隐式调用函数
				let fnName = Symbol('fn')
				context[fnName]=this;
				// 使用绑定的对象去调用方法
				let res=context[fnName](...callargs);
				// 因为绑定的时候算是给对象添加了属性func,所以需要删除该属性
				// 因为该属性不会再被用到
				delete context[fnName];
				return res
			}
```

## 手写apply
```
		Function.prototype.apply=function(context,args){
            if(!(this instanceof Function)){
                return new TypeError('不是函数')
            }
            context = context || window;
            args = args || []
            let fn = this;
            let fnName = Symbol('fn')
            context[fnName] = fn;
            let res= context[fnName](...args)
            delete context[fnName];
            return res;
        }
```

## 手写new运算符
```
			function newObj(){
            let args = Array.from(arguments) || [];
            if(!args.length){
                throw new Error('请传入构造函数')
            }
            if(!(args[0] instanceof Function)){
                throw new TypeError('类型错误，请传递构造函数')
            }
            let constructor=args.shift();
            let obj=Object.create(constructor.prototype)
            let res=constructor.apply(obj, args);
            // constructor构造函数return值不为对象则返回构造函数实例
            if(typeof res !== 'object'){
                return obj;
            }
            // 返回构造函数实际return值
            return res;
        }
        function fn(a,b){
            return {
                a:a,
                b:b
            }
        }
        let obj=newObj(fn,3,2)
        console.log(obj); // {a:3,b:2}
        
        function fn2(){
            return 2;
        }
        let obj2=newObj(fn2)
        console.log(obj2); // fn2{}
        
        function fn3(a,b){
            this.a=a;
            this.b=b;
        }
        let obj3=newObj(fn3,3,5)
        console.log(obj3); // fn3{a: 3, b: 5}
```


## 手写instanceof
```
			// instance就是判断一个函数 是否在另一个对象实例的原型链的构造函数中上
			function  isInstanceOf(instance, Func){
                if(typeof instance !== 'object'){
                    return false;
                }
                if(typeof Func !== 'object' && typeof Func !== 'function'){
                    return false;
                }
                let result = false;
                let proto = instance;
                while(proto!==null && proto.__proto__!==null){
                    if(proto.__proto__.constructor === Func){
                        result = true;
                        break;
                    } else {
                        proto = proto.__proto__; // 继续下一跳
                    }
                }
                return result;
            }
            let f = function () {console.log('f')}
            let ff = new f();
            console.log(isInstanceOf(ff, f));
            console.log(isInstanceOf(ff, Object));
            console.log(isInstanceOf(ff, null));
            let arr = [1]
            console.log(isInstanceOf(arr, Array));
            console.log(isInstanceOf(arr, f));
```

## 手写jsonp
```
// 手写jsonp,也就是在head标签处添加一个script标签，该标签的src能够返回一个js文件！
			(function jsonp(){
				let head=document.getElementsByTagName('head')[0]
				let script=document.createElement('script');
				script.src='https://res.wx.qq.com/open/libs/weuijs/1.2.1/weui.min.js'
				// 给该元素挂载加载完成后的回调事件
				script.addEventListener('load',(res)=>{
					console.log(res)
				})
				head.appendChild(script)
			})()
			// 如果要获取jsonp加载之后的数据，那么需要和后台配合，让后台返回回调函数
			
```

## 手写防抖
* 防抖就是不管事件触发多少次多久。上次事件触发完必须要要经过一段等待时间
* 如果低于等待时间，那么一定不触发防抖函数。必须在这段等待时间内不触发该事件！
```
			/**
         * @param fn {Function} 函数
         * @param time {number} 等待时间
         * @param immediate {Boolean} 是否立即触发函数
         * */
        function debounce(fn,time,immediate){
            let context=this;
            let timer;
            let func= function (){
                let args=arguments;
                if(timer){
                    clearTimeout(timer)
                }
                if(immediate){
                    // 等待时间已过！
                    if(!timer){
                        fn.apply(context,args);
                    }
                    timer = setTimeout(()=>{
                        timer=null;
                    },time)
                }else{
                    timer = setTimeout(()=>{
                        fn.apply(context,args)
                        timer = null;
                    },time)
                }
            }
            // 取消等待
            func.cancel = ()=>{
                if(timer){
                    clearTimeout(timer); // 不触发上个事件对应的函数
                    timer = null; // 等待时间设置为已过！
                }
            }
            return func;
        }
        let box=document.getElementById('box')
        function f(e){
            console.log(e,'click')
        }
        let res = debounce(f,1000,true)
        box.onclick = res;
        let btn=document.getElementById('btn');
        btn.onclick = res.cancel
```

## 节流
```
			// 节流(无论点击多少次，一定时间间隔内只触发一次)
			function throttle(fn,time,immediate){
            let context = this;
            let timer;
            let throttled=function (){
                if(timer){
                    return;
                }
                let args = arguments
                if(immediate){
                    fn.apply(context,arguments);
                    timer = setTimeout(()=>{
                        timer = null
                    },time)
                }else{
                    timer = setTimeout(()=>{
                        fn.apply(context,args);
                        timer=null;
                    },time)
                }
            }
            throttled.cancel=function (){
                if(timer){
                    clearTimeout(timer)
                    timer=null;
                }
            }
            return throttled
        }
        let box=document.getElementById('box')
        function f(e){
            console.log(e,'click')
        }
        let res = throttle(f,10000,true)
        box.onclick = res;
        let btn=document.getElementById('btn');
        btn.onclick = res.cancel
```

## 对象数组去重
```
			function destinct(arr){
				if(!(arr instanceof Array)){
					throw new TypeError('必须是数组')
				}
				var newarr=arr.map((item)=>{
					return JSON.stringify(item)
				})
				console.log(newarr)
				return [...(new Set(newarr))].map((item)=>{
					return JSON.parse(item)
				})
			}
			console.log(destinct([{e:3},{e:3},{e:5}]))
```

## promise实现红绿灯轮播
```
			// 使用promise实现一个圆(红绿灯) 红色1秒 绿色3秒 黄色5秒
			 var circle=document.getElementById('circle');
			 setInterval(function(){
				 new Promise(()=>{
					 circle.style.background='red'
					 console.log(new Date())
				 }).then(setTimeout(()=>{
					 circle.style.background='green'
					 console.log(new Date())
				 },1000)).then(setTimeout(()=>{
					 circle.style.background='yellow'
					 console.log(new Date())
				 },4000))
			 },9000)
```

## setTimeout实现setInterval
```
			class myinterval{
				constructor(){
					this.stop=false
				}
				on(func,wait){
					var that=this;
					var timer=setTimeout(function(){
						if(that.stop){
							clearTimeout(timer)
						}else{
							func();
							that.on(func,wait);	
						}
					},wait)
				}
				off(){
					console.log('关闭')
					this.stop=true;
				}
			}
			var left=document.getElementsByClassName('left')[0]
			var two=new myinterval()
			two.on(function(){
				console.log('使用setTimeout实现setInterval')
			},1000)
			setTimeout(function(){
				two.off();
			},3000)
```


## 手写map
```javascript
Array.prototype.map1=function (func){
            if(!Array.isArray(this)){
                throw  new Error('不是数组')
            }
            let arr=this;
            let newArr=[]
             arr.forEach((item,i)=>{
                newArr.push(func(item,i))
            })
            return newArr
        }
        let arr=[3,2,1]
        function pow(item){
            return Math.pow(item,2)
        }
        console.log(arr.map1(pow))
```

## 手写reduce
```javascript
/**
         * @param arr {Array<T>} 数组
         * @param func {Function(a,b)} 函数
         * @param initValue {Number} 初始值
         * */
        Array.prototype.reduce=function (func,initValue){
            let arr = this;
            if(arr.length===0){
                if(!isNaN(initValue)){
                    return initValue;
                }else{
                    throw  new Error('Uncaught TypeError: Reduce of empty array with no initial value')
                }
            }
            if(arr.length === 1 && initValue === undefined){
                return arr[0]
            }
            let result=0;
            for(let i=0;i<arr.length;i++){
                if(i===0){
                    if(initValue === undefined){
                        result = arr[0]
                    }else{
                        result = func.call(this,arr[0],initValue)
                    }
                    continue;
                }
                result=func.call(this,result,arr[i])
            }
            return result
        }
        let arr=[]
        console.log(arr.reduce1((a,b)=> a*b))
        console.log(arr.reduce1((a,b)=> a*b,10))
```
