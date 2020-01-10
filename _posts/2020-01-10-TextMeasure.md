---
layout: post
title: "Text测量"
description: "自定义View中，关于Text的测量"
tag: 自定义View 
---
## TextMeasure

在自定义View系列中，Text也算是一块难点，因为作为开发，经常会遇到一些奇葩的需求，涉及到Text的无序排版，如果我们没有掌握Text测量相关的技能，是无法面对这些需求的。

### drawText

```java
drawText(@NonNull String text, int start, int end, float x, float y, @NonNull Paint paint) 
```

这里需要知悉的一个点是，drawText是无法进行换行绘制的，即使是在绘制文字间加入了换行符\n，drawText的表现也只是在\n出绘制空格，如果需要自动换行，可以使用**StaticLayout**来进行文字的绘制，这个后面会说到。

### Paint Text相关属性及方法

#### 常用属性设置

```java
//设置字体尺寸
paint.setTextSize(60);
//设置字体
paint.setTypeface(Typeface.createFromAsset(getContext().getAssets(), "Satisfy-Regular.ttf"));
//设置对齐方式
paint.setTextAlign(Paint.Align.CENTER);
//设置字体加粗
paint.setFakeBoldText(true);
//添加删除线
paint.setStrikeThruText(true);
//添加下划线
paint.setUnderlineText(true);
//设置错切，制造斜体效果
paint.setTextSkewX(-0.5f);
//设置字体横向缩放
paint.setTextScaleX(1.2f);
```

说回drawText，参数里的x, y是指定文字绘制的坐标，但这里的坐标指的是baseline（基线）的坐标 ，而并不是绘制文字左上角的坐标，如下图：

![](https://struggledreamlin.github.io/images/posts/text_metrix.jpg)

看一下官方解释：

![](https://struggledreamlin.github.io/images/posts/fontMetrics.jpg)

这里的属性是[FontMetrics](https://developer.android.com/reference/android/graphics/Paint.FontMetrics.html#fields_1)的属性，后面文字的测量会说到。

### 测量

#### 1.paint.getTextWidths()，只测量宽度

   ```java
   String text = "abababg";
   float[] measureWidths = new float[text.length()];
   //measureLength 是测量的Str.length()
   int measureLength = paint.getTextWidths(text, measureWidths);
   ```

#### 2.paint.getTextBounds()，测量文字范围，获取的Rect即可测量宽度，也可测量高度

![](https://struggledreamlin.github.io/images/posts/text_rect.jpg)

```java
//需要注意的点是，这里的测量得到的rect，原点也是基于baseline,measureRect.top为负
Rect measureRect = new Rect();
paint.getTextBounds(text, 0, text.length(), measureRect);
```

#### 3.paint.getFontMetrics()，获取我们之前说到的FontMetrics.

```java
Paint.FontMetrics fontMetrics = new Paint.FontMetrics();
//metricsSpacing 这里返回的字段是 系统推荐的行距
float metricsSpacing = paint.getFontMetrics(fontMetrics);
```

什么是系统推荐的行距呢？看图：

![](https://struggledreamlin.github.io/images/posts/text_lineSpacing.jpg)

**leading:**这个属性大概是我们平时所习称为行间距的东西

所谓的系统推荐行距:

`lineSpaceing = descent - ascent + leading`

其实获取这个属性，我们直接调用paint.**getFontSpacing()**就可以得到。

#### 4.paint.breakText()

```java
float[] measureWidth = new float[1];
int start = 0;
int count = mPaint.breakText(text, start, text.length(), true, maxWidth, measureWidth);
```

实际上这个方法时用来测量截断位置的，就是在一个给定的宽度范围内显示一个字符串，如果字符串长度超过该范围，就在该范围能显示的最后一个字符处截断，并返回测量的字符串长度（字符数），一个典型的例子就是文字需要多行排版的时候，比如下图，我们需要在绘制文本在如下的View中，但是View中还需要放一张图片，那么文本的表现应该是不能遮挡图片的，遇到图片的时候换行显示。

![](https://struggledreamlin.github.io/images/posts/text_composing.jpg)

```java
int width = getWidth();
int imageTop = (int) Utils.dp2px(40);
canvas.drawBitmap(bitmap, width - bitmap.getWidth() - IMAGE_PADDING,
        imageTop + IMAGE_PADDING, mPaint);
int start = 0;
int count;
for (int i = 0; start < text.length(); i++) {
    float textY = mPaint.getFontSpacing() * (i + 1);
    float maxWidth = textY > imageTop && textY < (imageTop + (IMAGE_PADDING << 1) + bitmap.getHeight() + mPaint.getFontSpacing()) ?
            (width - bitmap.getWidth() - (IMAGE_PADDING << 1)) : width;
    count = mPaint.breakText(text, start, text.length(), true, maxWidth, measureWidth);
    canvas.drawText(text, start, start + count, 0, textY, mPaint);
    start += count;
}
```

![](https://struggledreamlin.github.io/images/posts/text_composing_result.jpg)






