VDOM和真实DOM的区别和优化:
1. 虚拟DOM不会立马进行排版与重回操作
2. 虚拟DOM进行频繁修改， 然后一次性比较并修改真实DOM中需要改的部分,最后在真实DOM中进行排版与重绘， 减少过多DOM节点排版与重绘损耗
3. 虚拟DOM有效降低大面积真实DOM的重绘与排版， 因为最终与真实DOM比较差异， 可以只渲染局部

