# this

##### 模拟isNaN实现

isNaN方法用于判断一个值被转换为数字后是否是NaN【是NaN返回true】

<img src="第十节.assets/image-20220325154706255.png" alt="image-20220325154706255" style="zoom:67%;" /> 

### this在不同场景中的指向

1. 函数预编译过程：this ---> window

   - ##### new关键字原理

   - <img src="第十节.assets/image-20220325165329581.png" alt="image-20220325165329581" style="zoom:67%;" /> 

2. 全局作用域：this ---> window / GO

   - <img src="第十节.assets/image-20220325165817385.png" alt="image-20220325165817385" style="zoom:50%;" /> 

3. call / apply 改变函数运行时this指向：this ---> 第一个参数

4. 调用对象中的方法：this ---> 调用者 / 对象

案例1：

<img src="第十节.assets/image-20220325173630596.png" alt="image-20220325173630596" style="zoom:67%;" />  

案例2：

<img src="第十节.assets/image-20220328153736018.png" alt="image-20220328153736018" style="zoom:67%;" /> 

 <img src="第十节.assets/image-20220328154005369.png" alt="image-20220328154005369" style="zoom: 67%;" /> 

<img src="第十节.assets/image-20220328154927923.png" alt="image-20220328154927923" style="zoom: 50%;" /> 

案例3：

<img src="第十节.assets/image-20220328155417323.png" alt="image-20220328155417323" style="zoom:50%;" /> <img src="第十节.assets/image-20220328155609708.png" alt="image-20220328155609708" style="zoom:50%;" /> 

案例4：

<img src="第十节.assets/image-20220328160023071.png" alt="image-20220328160023071" style="zoom:67%;" /> 

案例5：

<img src="第十节.assets/image-20220328163119979.png" alt="image-20220328163119979" style="zoom:67%;" /> 

案例6：

<img src="第十节.assets/image-20220328164001836.png" alt="image-20220328164001836" style="zoom:67%;" /> 

### callee和caller

callee：arguments.callee指向当前函数在内存中的引用，即：等于函数名。

- <img src="第十节.assets/image-20220325174213833.png" alt="image-20220325174213833" style="zoom:50%;" /> 
- 案例：立即执行函数利用递归实现阶乘
  - <img src="第十节.assets/image-20220325174443295.png" alt="image-20220325174443295" style="zoom: 50%;" /> <img src="第十节.assets/image-20220325174703704.png" alt="image-20220325174703704" style="zoom:50%;" /> 

caller：fn.caller指向fn函数执行时的环境

- <img src="第十节.assets/image-20220325175402175.png" alt="image-20220325175402175" style="zoom:50%;" /> 

注意：在ES5严格模式下，arguments 和 caller 不允许使用。







