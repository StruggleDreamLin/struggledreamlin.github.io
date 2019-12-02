---
layout: post
title: "属性动画"
description: "Android动画相关"
tag: 自定义View 
---
## 属性动画


### ViewPropertyAnimator

​	View类会有一个animate()方法，该方法返回一个ViewPropertyAnimator对象，而ViewPropertyAnimator和ObjectAnimator一样都是view.animate()（属性动画）的子类，我们可以使用该对象来对View执行一些动画

```java
public ViewPropertyAnimator animate() {
    if (mAnimator == null) {
        mAnimator = new ViewPropertyAnimator(this);
    }
    return mAnimator;
}
```

我们要实现一些基本的动画就可以通过调用该方法获得ViewPropertyAnimator对象，然后使用对应的动画执行方法来进行一些简单的动画
- 平移

  view.animate().translationX(positionX);

  view.animate().translationY(positionY);

  //Z轴的平移可以配合outline实现阴影效果

  view.animate().translationZ(positionZ);

  //后面有By的方法，是相对当前位置的平移

  view.animate().translationXBy(positionX);

  view.animate().translationYBy(positionY);

  view.animate().translationZBy(positionZ);

- 旋转

  //绕Z轴旋转

  view.animate().rotation(angle);

  view.animate().rotationX(angle);

  view.animate().rotationY(angle);

  //后面有By的方法，是相对当前角度的旋转

  view.animate().rotationBy(angle);

  view.animate().rotationXBy(angle);

  view.animate().rotationYBy(angle);

- 缩放

  view.animate().scaleX(scaleX)

  view.animate().scaleY(scaleY)

  view.animate().scaleByX(scaleX)

  view.animate().scaleByY(scaleY)

- 透明

  view.animate().alpha(alpha)

  view.animate().alphaBy(alpha)

### ObjectAnimator

- 创建动画对象

```java
public static ObjectAnimator ofFloat(Object target, String propertyName, float... values) {
    ObjectAnimator anim = new ObjectAnimator(target, propertyName);
    anim.setFloatValues(values);
    return anim;
}
```

### 周期

设置动画执行时间:

```java
objectAnimator.setDuration(500)
```

### 内插器

用来控制动画的效果

```java
objectAnimator.setInterpolator(new XXXInterpolator)
```

| Interpolator                     | Description                                                  |
| -------------------------------- | ------------------------------------------------------------ |
| AccelerateDecelerateInterpolator | 默认就是该内插器，先加速后减速，同样也符合我们现实中的物理规则 |
| LinearInterpolator               | 匀速内插器                                                   |
| AccelerateInterpolator           | 持续加速                                                     |
| DecelerateInterpolator           | 持续减速                                                     |
| AnticipateInterpolator           | 先回拉，然后再开始正常动画，类似蓄力效果，比如投掷物体，射箭等 |
| OvershootInterpolator            | 到达目标位置后会超出一部分，然后回弹回来                     |
| AnticipateOvershootInterpolator  | 上面两个的结合体                                             |
| BounceInterpolator               | 在目标值位置弹跳，类似乒乓球落地                             |
| CycleInterpolator                | 这个也是一个正弦 / 余弦曲线，不过它和 `AccelerateDecelerateInterpolator` 的区别是，它可以自定义曲线的周期，所以动画可以不到终点就结束，也可以到达终点后回弹，回弹的次数由曲线的周期决定，曲线的周期由 `CycleInterpolator()` 构造方法的参数决定 |
| PathInterpolator                 | 自定义动画完成度 / 时间完成度曲线。用这个 `Interpolator` 你可以定制出任何你想要的速度模型。定制的方式是使用一个 `Path` 对象来绘制出你要的动画完成度 / 时间完成度曲线。 |
| FastOutLinearInInterpolator      | 其实它和 `AccelerateInterpolator` 一样，都是一个持续加速的运动路线。只不过 `FastOutLinearInInterpolator` 的曲线公式是用的贝塞尔曲线，而 `AccelerateInterpolator` 用的是指数曲线。具体来说，它俩最主要的区别是 `FastOutLinearInInterpolator` 的初始阶段加速度比 `AccelerateInterpolator` 要快一些。 |
| FastOutSlowInInterpolator        | 同样也是先加速再减速的还有前面说过的 `AccelerateDecelerateInterpolator`，不过它们的效果是明显不一样的。`FastOutSlowInInterpolator` 用的是贝塞尔曲线，`AccelerateDecelerateInterpolator` 用的是正弦 / 余弦曲线。具体来讲， `FastOutSlowInInterpolator` 的前期加速度要**快得多**。 |
| LinearOutSlowInInterpolator      | 它和 `DecelerateInterpolator` 比起来，同为减速曲线，主要区别在于 `LinearOutSlowInInterpolator` 的初始速度更高。对于人眼的实际感觉，区别其实也不大，不过还是能看出来一些的。 |
|                                  |                                                              |

  内容只要摘录于[Hencoder 1-6](https://hencoder.com/ui-1-6/)



 

Image:

![](https://struggledreamlin.github.io/images/posts/非对称加密阿里.png)

### TODO:

这里先记一些很需要去记录的点吧，可能时间忙不过来，慢慢补上。。。

- CoordinatorLayout相关使用及自定义

- 动态代理

  