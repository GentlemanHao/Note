获取子`View`最终调用了`LayoutManeger`中的`LayoutState的next(RecyclerView.Recycler recycler)`，然后判断是否显示动画效果，是的话则直接从`mScrapList`获取，否则通过缓存机制去获取。

**mAttachedScrap**：用于缓存显示在屏幕上并符合对应规则的`ViewHolder`

**mChangeScrap**：用于缓存显示在屏幕上不符合`mAttachedScrap`规则的`ViewHolder`

**mCacheViews**：保存滑动过程中从可见到不可见的`ViewHolder`，大小为`DEFAULT_CACHE_SIZE = 2`，并且原本数据信息都在，所以可以直接添加到`RecyclerView`中显示，不需要重新`onBindViewHolder`，在数据多的时候，滑动过程中`size`会变成3

**mHiddenViews**：保存在添加或者删除数据或其他操作，保存在屏幕内可见的带动画子View

缓存机制：

首先判断是不是执行动画的预布局，是的话则尝试从`mChangedScrap`中获取

然后尝试从`mAttachedScrap`、`mHiddenViews`、`mCacheViews`获取

然后根据`mAdapterhasStablelds`以及`mViewCacheExtension  != null`判断是否从用户自定义设置的相关逻辑中获取

然后再尝试从缓存池`RecycledViewPool`中`mScrap`的`ScrapData`中获取，每种样式最多缓存5个

还没有则通过`mAdapter.createViewHolder`创建新的

https://juejin.im/post/5db7eaa96fb9a0203f6f9ca6



**拉勾课程**

RecyclerView的缓存核心代码在内部类Recycler中完成，主要用来缓存屏幕内的ViewHolder和屏幕外的ViewHolder

![](https://s0.lgstatic.com/i/image/M00/09/9D/Ciqc1F68ukSAZT3uAAD9_pc55Io230.png)

缓存共分为4级

![](https://s0.lgstatic.com/i/image/M00/09/9D/Ciqc1F68ukuAM0LVAACHb_a34AY925.png)









