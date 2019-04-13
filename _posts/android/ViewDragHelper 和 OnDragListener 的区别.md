## ViewDragHelper 和 OnDragListener 的区别



ViewDragHelper 类似属性动画，改变属性进行拖拽；

onDragListener 创建一个图像，产生拖拽效果；

所以ViewDragHelper会导致原图改变，onDragListener不会导致原图改变。

onDragListener 设计时为了拖动一些需要的数据到其他地方，拖动到图像可以自定义。