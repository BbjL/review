### JSON转换

```
1. JSON.stringify(obj/arr)
  * js对象(数组)转换为json对象(数组)
2. JSON.parse(json)
  * json对象(数组)转换为js对象(数组)

```

### ### 对象扩张

```
ES5给Object扩展了好一些静态方法, 常用的2个:
1. Object.create(prototype, [descriptors])
  * 作用: 以指定对象为原型创建新的对象
  * 为新的对象指定新的属性, 并对属性进行描述
    value : 指定值
    writable : 标识当前属性值是否是可修改的, 默认为false
    configurable: 标识当前属性是否可以被删除 默认为false
    enumerable： 标识当前属性是否能用for in 枚举 默认为false

2. Object.defineProperties(object, descriptors)
  * 作用: 为指定对象定义扩展多个属性
  * get ：用来获取当前属性值得回调函数
  * set ：修改当前属性值得触发的回调函数，并且实参即为修改后的值
  * 存取器属性：setter,getter一个用来存值，一个用来取值
  
3、对象本身的两个方法
  * get propertyName(){} 用来得到当前属性值的回调函数
  * set propertyName(){} 用来监视当前属性值变化的回调函数
  
  
4. Object.is(v1, v2)
  * 判断2个数据是否完全相等
5. Object.assign(target, source1, source2..)
  * 将源对象的属性复制到目标对象上
3. 直接操作 __proto__ 属性
  let obj2 = {};
  obj2.__proto__ = obj1;
  
  
  
   	var obj = {name : 'curry', age : 29}
    var obj1 = {};
    obj1 = Object.create(obj, {
        sex : {
            value : '男',
            writable : true
        }
    });
    obj1.sex = '女';
    console.log(obj1);
    
    
    //Object.defineProperties(object, descriptors)
    var obj2 = {
        firstName : 'curry',
        lastName : 'stephen'
    };
    Object.defineProperties(obj2, {
        fullName : {
            get : function () {
                return this.firstName + '-' + this.lastName
            },
            set : function (data) {
                var names = data.split('-');
                this.firstName = names[0];
                this.lastName = names[1];
            }
        }
    });
    console.log(obj2.fullName);  //curry-stephen
    obj2.firstName = 'tim';
    obj2.lastName = 'duncan';	
    console.log(obj2.fullName);		//tim-duncan
    obj2.fullName = 'kobe-bryant';
    console.log(obj2.fullName);		
    
   //
   var obj = {
        firstName : 'kobe',
        lastName : 'bryant',
        get fullName(){
            return this.firstName + ' ' + this.lastName
        },
        set fullName(data){
            var names = data.split(' ');
            this.firstName = names[0];
            this.lastName = names[1];
        }
    };
    console.log(obj.fullName);
    obj.fullName = 'curry stephen';
    console.log(obj.fullName);
```

### 字符串扩展

```
1. includes(str) : 判断是否包含指定的字符串
2. startsWith(str) : 判断是否以指定字符串开头
3. endsWith(str) : 判断是否以指定字符串结尾
4. repeat(count) : 重复指定次数
```

### 数组扩展

```
Array.prototype.indexOf(value) : 得到值在数组中的第一个下标2. Array.prototype.lastIndexOf(value) : 得到值在数组中的最后一个下标3. Array.prototype.forEach(function(item, index){}) : 遍历数组4. Array.prototype.map(function(item, index){}) : 遍历数组返回一个新的数组，返回加工之后的值5. Array.prototype.filter(function(item, index){}) : 遍历过滤出一个新的子数组， 返回条件为true的值

Array.from(v) : 将伪数组对象或可遍历对象转换为真数组
Array.of(v1, v2, v3) : 将一系列值转换成数组
find(function(value, index, arr){return true}) : 找出第一个满足条件返回true的元素
findIndex(function(value, index, arr){return true}) : 找出第一个满足条件返回true的元素下标
```

### 函数扩展

```
1. Function.prototype.bind(obj) :
  * 作用: 将函数内的this绑定为obj, 并将函数返回
2. 面试题: 区别bind()与call()和apply()?
  * 都能指定函数中的this
  * call()/apply()是立即调用函数
  * bind()是将函数返回
  
    function fun(age) {
        this.name = 'kobe';
        this.age = age;
        console.log('dddddddddddddd');
    }
    var obj = {};
    fun.bind(obj, 12)();
    console.log(obj.name, obj.age);
```

### 数值扩展

```
1. 二进制与八进制数值表示法: 二进制用0b, 八进制用0o
2. Number.isFinite(i) : 判断是否是有限大的数
3. Number.isNaN(i) : 判断是否是NaN
4. Number.isInteger(i) : 判断是否是整数
5. Number.parseInt(str) : 将字符串转换为对应的数值
6. Math.trunc(i) : 直接去除小数部分
```

### 深浅拷贝

```
  1、数据类型：
    * 数据分为基本的数据类型(String, Number, boolean, Null, Undefined)和对象数据类型
    - 基本数据类型：
      特点： 存储的是该对象的实际数据
    - 对象数据类型：
      特点： 存储的是该对象在栈中引用，真实的数据存放在堆内存里
  2、复制数据
    - 基本数据类型存放的就是实际的数据，可直接复制
      let number2 = 2;
      let number1 = number2;
    - 克隆数据：对象/数组
      1、区别： 浅拷贝/深度拷贝
         判断： 拷贝是否产生了新的数据还是拷贝的是数据的引用
         知识点：对象数据存放的是对象在栈内存的引用，直接复制的是对象的引用
         let obj = {username: 'kobe'}
         let obj1 = obj; // obj1 复制了obj在栈内存的引用
      2、常用的拷贝技术
        1). arr.concat(): 数组浅拷贝
        2). arr.slice(): 数组浅拷贝
        3). JSON.parse(JSON.stringify(arr/obj)): 数组或对象深拷贝, 但不能处理函数数据
        4). 浅拷贝包含函数数据的对象/数组
        5). 深拷贝包含函数数据的对象/数组
        
// typeof instanceof Object.prototype.toString.call 判断数据类型
//https://juejin.im/post/6844903728739254286
  function getObjClass(obj) {   // 检测数据类型
    let result = Object.prototype.toString.call(obj).slice(8, -1);
    if(result === 'Null'){
      return 'Null';
    }else if(result === 'Undefined'){
      return 'Undefined';
    }else {
      return result;
    }
  }
  
 // 深度克隆
  function deepClone(obj) {
    let result, objClass = getObjClass(obj);
    if(objClass === 'Object'){
      result = {};
    }else if(objClass === 'Array'){
      result = [];
    }else {
      return obj; // 如果是其他数据类型不复制，直接将数据返回
    }
    // 遍历目标对象
    for(let key in obj){
      let value = obj[key];
      if(getObjClass(value) === "Object" || 'Array'){
        result[key] = deepClone(value);
      }else {
        result[key] = obj[key];
      }
    }
    return result;
  }
  
  let obj3 = {username: 'kobe',age: 39, sex: {option1: '男', option2: '女'}};
  let obj4 = deepClone(obj3);
  console.log(obj4);
  obj4.sex.option1 = '不男不女'; // 修改复制后的对象不会影响原对象
  console.log(obj4, obj3);
```

