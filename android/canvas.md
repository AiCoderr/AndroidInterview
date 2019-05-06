# Canvas使用

  - **save**：用来保存 Canvas 的状态。save 之后，可以调用 Canvas 的平移、放缩、旋转、错切、裁剪等操作。

  - **restore**：用来恢复Canvas之前保存的状态。防止 save 后对 Canvas 执行的操作对后续的绘制有影响。


save 和 restore 要配对使用( restore 可以比 save 少，但不能多)，如果 restore 调用次数比 save 多，会引发 Error 。save 和 restore 之间，往往夹杂的是对 Canvas 的特殊操作。

* [绘制基础](https://hencoder.com/ui-1-1/)

* [Paint详解](https://hencoder.com/ui-1-2/)

* [文字的绘制](https://hencoder.com/ui-1-3/)

* [范围裁切和几何变换](https://hencoder.com/ui-1-4/)

* [绘制顺序](https://hencoder.com/ui-1-5/)

* [属性动画](https://hencoder.com/ui-1-6/)

* [自定义布局](https://hencoder.com/ui-2-1/)
1.  测量阶段，measure()方法被父View调用，在measure()中做一些准备和优化工作后，调用onMeasure()来进行实际的自我测量。onMeasure()做的事，View和ViewGroup不一样
    `View`：view在onMeasure()方法中会计算出自己的尺寸，然后保存
    `ViewGroup`：ViewGroup在onMeasure()方法中会调用所有子View的measure()方法让他们行
    自我测量，并根据子View计算出的期望尺寸来计算出它们的实际尺寸和位置(实际上 99.99% 
    的父 View 都会使用子 View 给出的期望尺寸来作为实际尺寸)然后保存。同时，它也会根据子
    View的尺寸和位置来计算出自己的尺寸然后保存
    
2. 布局阶段，layout()方法被父View调用，在layout()中它会保存父View传进来的自己的位置和尺寸，并且调用onLayout()来进行实际的内部布局。onLayout()做的事，View和ViewGroup也不一样

   `View`：由于没有子View，所以View的onLayout()什么也不做

   `ViewGroup`：ViewGroup会在onLayout()中调用所有子View的layout()方法，把它们的尺寸和位置传给它们，让它们完成自我的内部布局

3. 布局过程自定义的方式(三类)
   方式一：重写`onMeasure()`来修改已有的View的尺寸
      S1：重写 `onMeasure()` 方法，并在里面调用 `super.onMeasure()`，触发原有的自我测量
      S2：在 `super.onMeasure()` 的下面用 `getMeasuredWidth()` 和 `getMeasuredHeight()` 来获取到之前的测量结果，并使用自己的算法，根据测量结果计算出新的结果
      S3：调用 `setMeasuredDimension()` 来保存新的结果
   
   方式二：重写`onMeasure()`来全新定制自定义View的尺寸
       S1：重写 `onMeaure()`，并计算出 View 的尺寸(宽高值和各种padding值)
       S2：把计算的结果用`resolveSize()`过滤一遍然后保存
       S3：调用 `setMeasuredDimension()` 来保存新的结果
   
   方式三：重写`onMeasure()`和`onLayout()`来全新定制自定义ViewGroup的内部布局
       S1：重写onMeasure来计算内部布局
          (1)调用每个子View的measure()来计算子View的尺寸
          (2)计算子View的位置并保存子View的位置和尺寸
          (3)计算自己的尺寸并调用`setMeasuredDimension()`保存
       S2：重写onLayout()来摆放子View(调用子View的layout方法)