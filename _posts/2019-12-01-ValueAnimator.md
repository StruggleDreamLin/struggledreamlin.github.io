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

| 创建动画对象                                                 |      |
| ------------------------------------------------------------ | ---- |
| ObjectAnimator ofInt(Object target, String propertyName, int... values) |      |
| ObjectAnimator ofFloat(Object target, String propertyName, float... values) |      |
| ObjectAnimator ofArgb(Object target, String propertyName, int... values) |      |
| ObjectAnimator ofObject(Object target, String propertyName,        TypeEvaluator evaluator, Object... values) |      |
| ObjectAnimator ofMultiInt(Object target, String propertyName, int[][] values) |      |
| ObjectAnimator ofMultiFloat(Object target, String propertyName,        float[][] values) |      |



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

  内容主要摘录于[Hencoder 1-6](https://hencoder.com/ui-1-6/)，感谢大佬[扔物线]()的分享

### 监听器

- addListener
- addUpdateListener
- addPauseListener

###  进阶用法

- 自定义动画属性

  我们大概都使用过的：

  ```java
  ObjectAnimator objectAnimator = ObjectAnimator.ofFloat(view, "translationX", 500);
  ```

  实际上是ObjectAnimator通过translationX的setter和getter方法，对view的translationX做了实际的修改，从而实现了属性动画，在动画执行时，修改实际的属性值；所以我们要实现自定义属性，首先需要定义动画目标需要跟随动画改编的属性，然后提供其getter和setter方法，并在setter执行后对目标对象作出对应的处理：如下示例:

  比如我要对一个View实现颜色的渐变（当然Android有提供ObjectAnimator.ofArgb来实现，这里只是举例），颜色我们都知道可以用int来表示:

  ![自定义颜色](https://struggledreamlin.github.io/images/posts/animator_custom_color.png)

  接下来在onDraw函数中绘制出颜色值

  ```java
  @Override
  protected void onDraw(Canvas canvas) {    
      super.onDraw(canvas);    
      if (color != -1)        
          canvas.drawColor(color);
  }
  ```

  我们在另外一个地方来创建CustomPropertyView

  ```java
  customPropertyView = findViewById(R.id.custom_view);
  btnStart = findViewById(R.id.btn_start);
  ObjectAnimator animator = ObjectAnimator.ofInt(customPropertyView, "color",        0xFFFF0000, 0xFF0000FF);
  //设置ArgbEvaluatorCompat，使得颜色值能够正确的转换；否则颜色值会被当成一个整数来处理，达不到我们预期的效果
  animator.setEvaluator(new ArgbEvaluatorCompat());
  animator.setDuration(3000);
  btnStart.setOnClickListener(v -> animator.start());
  ```

  效果:

  ![](https://struggledreamlin.github.io/images/posts/animator_custom_effect.gif)

- 自定义动画对象及属性

  实际上，属性动画可以可以作用于任何的目标对象，之前列出来的属性动画创建方式中有这一条：

  ObjectAnimator.ofObject(Object target, String propertyName, TypeEvaluator evaluator, Object... values)

  可以看到这里多了一个参数，TypeEvaluator，其实我们在上面用到过：

  animator.setEvaluator(new ArgbEvaluatorCompat());

  这个其实可以理解为adapter（适配器），他是动画完成度（fraction）和 属性值（value）的适配器，定义了value随动画完成度如何变化。Android提供了一些Evaluator，比如之前用到的ArgbEvaluatorCompat；当我们需要自定义动画对象及其属性的时候，可以通过扩展 Evaluator（继承Evaluator）来实现我们想要实现的效果，这个就不举例了，实际案例可以参考官方提供的 PointFEvaluator 实现（动画对象是 Point）;

- 多属性变化

  实际使用中，我们往往会对一个动画对象同时变更多个属性，比如同时移动x坐标，y坐标，同时还要做透明的变化，这时我们应该使用PropertyValuesHolder来实现。

  ```java
  PropertyValuesHolder holder1 = PropertyValuesHolder.ofFloat("translationX", 300, 500);
  PropertyValuesHolder holder2 = PropertyValuesHolder.ofFloat("translationY", 400, 88);
  PropertyValuesHolder holder3 = PropertyValuesHolder.ofFloat("alpha", 0, 1);
  //使用ofofPropertyValuesHolder结合多个变换同时执行
  ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder1, holder2, holder3);
  animator.start();
  ```

- 多动画集合

  ```java
  ObjectAnimator animator1 = ObjectAnimator.ofFloat(view, "alpha", 0, 1);
  ObjectAnimator animator2 = ObjectAnimator.ofFloat(view, "translationX", -200, 200);
  ObjectAnimator animator3 = ObjectAnimator.ofFloat(view, "rotation", 0, 720);
  //可以为每个动画指定不同的执行时长
  animator1.setDuration(500);
  animator2.setDuration(1000);
  animator3.setDuration(1000);
  AnimatorSet animatorSet = new AnimatorSet();
  animatorSet.play(animator2)
             .with(animator3)
             .after(animator1);
  //执行顺序 1 2&3
  animatorSet.start();
  ```

- 动画拆解

  PropertyValuesHolder除了合并属性之外还可以拆解属性：

  ```java
  Keyframe keyframe1 = Keyframe.ofFloat(0, 0);
  //动画到一半时完成100%
  Keyframe keyframe2 = Keyframe.ofFloat(0.5f, 100);
  //动画完成时回弹到80%
  Keyframe keyframe3 = Keyframe.ofFloat(1, 80);
  PropertyValuesHolder holder = PropertyValuesHolder.ofKeyframe("progress", keyframe1,        keyframe2, keyframe3);
  ValueAnimator valueAnimator = ObjectAnimator.ofPropertyValuesHolder(view, holder);
  valueAnimator.setDuration(2000);valueAnimator.start();
  ```

效果：

![](https://struggledreamlin.github.io/images/posts/keyframe.gif)



Ok，至此属性动画记录完毕，以上内容参考[HenCoder系列](https://hencoder.com/ui-1-7/)，感谢扔物线大佬。

