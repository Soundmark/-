# 什么是纵深感强的背景？

[csdn地址](https://blog.csdn.net/weixin_47492955/article/details/107745880)

![在这里插入图片描述](https://img-blog.csdnimg.cn/202008021555088.jpg)
像上图这样的看起来空间感很强的图片就是纵深感比较强的，让人感觉会比较有空间感，像我们平时写个人网站纯色背景或许不能给人一种印象深刻的感觉，但是如果用上了有纵深感的背景就会呈现出立体感，让人感觉很舒服。
有人可能会说，做一个背景的话直接去找一张有纵深感的图片就行了，但是别忘了，图片不一定了很好地适配各种分辨率的显示器，而且作为背景的话，图片肯定是需要清晰的，一张高清的图片可不小，如果你的服务器一般的话，下载一张大图片是要花不少时间的，这样就造成一种不好的用户体验，所以我这里说的是要用代码写出来。

## 思路分析
我用photoshop做了一个纵身背景：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200802161004909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
说到底，纵深感就是空间感，你要用平面去营造一种空间的感觉，像上图，就像一个房间，如果把内容放到中间，就会起到突出效果。
我们可以把他分成6个矩形：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200802162354285.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
通过这样的一种组合就能形成空间感，所以我们只需要通过几个div盒子叠加就能形成这样的效果。

## 代码实现
html：

```html
<!--容器，对应最大的矩形-->
<div class="bigBG">
  <!--四个角对应的矩形-->
  <div class="linear topright"></div>
  <div class="linear1 bottomright"></div>
  <div class="linear2 bottomright"></div>
  <div class="linear3 topright"></div>
</div>
```

css:

```css
.bigBG{
  width: 100%;
  height: 100vh;
  min-width: 1350px;
  min-height: 750px;
  position: relative;
  background-color: #545252;
  box-shadow: inset 0 0 110px 25px white;/**重点，给容器加一个内阴影，形成四周白边的效果**/
}
.bigBG .linear{
  position: absolute;
  left: 0;
  top: 0;
}
.bigBG .linear1{
  position: absolute;
  left: 0;
  bottom: 0;
}
.bigBG .linear2{
  position: absolute;
  right: 0;
  top: 0;
}
.bigBG .linear3{
  position: absolute;
  right: 0;
  bottom: 0;
}
.bigBG .topright{
  /**边角的矩形添加对角线效果**/
  background: linear-gradient(
    to top right,
    rgba(0,0,0,0) 0%,
    rgba(84,82,82,1) 50%,
    rgba(0,0,0,0) 100%
  );
  /**重点，矩形尺寸要设置好，需要使四个矩形边有重合的地方，不然四个边角矩形会穿帮**/
  width: 50%;
  height: 500px;
}
.bigBG .bottomright{
  background: linear-gradient(
    to bottom right,
    rgba(0,0,0,0) 0%,
    rgba(84,82,82,1) 50%,
    rgba(0,0,0,0) 100%
  );
  width: 50%;
  height: 500px;
}

```

通过上面的代码，就能做出刚刚ps图的效果了：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200802164708692.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
加上内容：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200802164857639.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzQ5Mjk1NQ==,size_16,color_FFFFFF,t_70)
是不是有一种突出内容的效果，哈哈。上面是我的摄影博客，虽然暂时并不是很多内容，但是欢迎大家过来踩踩！地址：[www.doglefts.cn](https://www.doglefts.cn)