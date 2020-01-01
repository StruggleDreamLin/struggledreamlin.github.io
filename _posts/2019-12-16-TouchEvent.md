---
layout: post
title: "自定义View之触摸反馈"
description: "Android自定义绘制的触摸反馈部分"
tag: 自定义View
---
## 触摸反馈



### 事件分发机制

​	事件是成组存在的，如：

`ACTION_DOWN -> ACTION_MOVE -> ACTION_MOVE -> ACTION_MOVE -> ACTION_UP`

`ACTION_DOWN -> ACTION_UP`

`ACTION_DOWN -> ACTION_MOVE -> ACTION_MOVE -> ACTION_MOVE -> ACTION_CANCEL`

- onTouchEvent: 触摸事件会从最顶层的View开始询问View是否需要消费该事件，如果View消费该事件，事件流在这里就结束了，反之如果View不消费该事件，则事件将继续向下传递，直到最底层的RootView。需要注意的一点是，**是否消费该组事件需要在ACTION_DOWN 事件中决定**，如果在 **ACTION_DOWN**事件中return了false，那么后续的事件都无法进行处理了。
- onInterceptTouchEvent: 其实该方法会在onTouchEvent之前执行，不同的是onInterceptTouchEvent是从最底层的RootViewGroup开始向上进行询问ViewGroup，是否要拦截该事件流，如果拦截，则ViewGroup对于该事件流自己进行处理，不会交给子View去处理。和onTouchEvent不同的是onInterceptTouchEvent不需要在 **ACTION_DOWN** 事件中就决定是否消费一组事件，onInterceptTouchEvent可以针对每个事件进行拦截处理，并且在拦截时会向子View发送一个 **ACTION_CANCEL** 事件，比如子View在onTouchEvent 中的**ACTION_DOWN** 事件return 了ture，但在随后的 **ACTION_MOVE** 事件却被ViewGroup拦截了，所以此时需要ViewGroup给子View发送一个**ACTION_CANCEL**事件。
- requestDisallowInterceptTouchEvent：还存在的一种情况是子View不希望ViewGroup拦截自己的事件，那么只需要调用requestDisallowInterceptTouchEvent方法，传入true即可。
- dispatchTouchEvent: 事件分发总调度方法，一个事件分发的过程就是从RootView开始递归调用了dispatchTouchEvent的过程。

### 自定义触摸反馈

- 重写onTouchEvent，在**ACTION_DOWN事件中返回true**，在对应的事件中写自己的触摸反馈算法。
- ViewGroup需要拦截事件的，**重写onInterceptTouchEvent，在合适的事件中返回true**，拦截事件，实现自己的处理。
- 存在ViewGroup的事件拦截时，此时子View并不希望被拦截事件，可以调用**requestDisallowInterceptTouchEvent** 阻止ViewGroup拦截自己的事件。

### 练习项目

- [薄荷健康 卷尺效果](https://gitee.com/struggledreamlin/ruler.git)

