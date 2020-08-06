## js任务队列
js的任务分为**同步任务**和**异步任务**，异步任务又分为**宏任务**和**微任务**。

### 同步任务
众所周知，js是单线程运行的，在js主线程上的任务是会排队一个一个执行的，也就是所谓的阻塞，简单来说，运行在主线程上的任务就是同步任务。

### 异步任务
异步任务又分为宏任务和微任务，宏任务和微任务不会立即执行，会先被放到event table中注册，然后再推到各自的event queue，待主线程的同步任务执行完，再去执行event queue里的微任务和宏任务。

## 事件循环
js的事件循环流程如下：
1.同步任务
2.微任务
3.宏任务
4.微任务
5.然后再3和4循环执行

下面我用settimeout代指宏任务，promise代指微任务（注意创建promise实例对象的过程是同步任务，promise的then才是微任务），分析事件循环流程：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200807000039894.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200807000051354.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200807000059341.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
接下来用一段代码验证一下：

```javascript
  setTimeout(() => {
    console.log('这是宏任务')
  }, 0);
  
  new Promise((resolve)=>{
    resolve(1)
  }).then(()=>{
    console.log('这是微任务1')
  }).then(()=>{
    console.log('这是微任务2')
  })

  console.log('这是主线程的同步任务')

  new Promise((resolve)=>{
    resolve(1)
  }).then(()=>{
    console.log('这是微任务3')
  }).then(()=>{
    console.log('这是微任务4')
  })
```
流程一：
首先遇到settimeout，settimeout是宏任务，所以放入宏任务队列，然后遇到promise，执行到微任务1，放进微任务队列，然后继续执行主线程的同步任务，输出“这是主线程的同步任务”，接下来遇到第二个promise，执行到微任务3，放进微任务队列。

流程二：
检查微任务队列，发现微任务1和微任务3，放进主线程执行，微任务1输出“这是微任务1”，然后执行到微任务2，放进微任务队列，接着执行微任务3，输出“这是微任务3”，然后执行到微任务4，放进微任务队列。

流程三：
再次检查微任务队列，发现微任务2和微任务4，放进主线程执行，微任务2输出“这是微任务2”，微任务4输出“这是微任务4”，微任务队列还行完毕。

流程四：
检查宏任务队列，发现宏任务，执行输出“这是宏任务”，至此，整个流程结束。

输出结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200807001005770.png)
