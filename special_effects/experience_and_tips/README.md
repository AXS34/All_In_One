# 一些经验和技巧

[外部导入模型清除多余数据技巧](https://www.bilibili.com/video/av57753966)-----9'06''

[group和attribute的正则表达式](https://zhuanlan.zhihu.com/p/80050303)

[海面由粒子体积转为平面](https://vod1.yiihuu.com/vod/video_mp4/6598/f56875a6f17c109e15d1f4fc9f0171fd-sd-130484.mp4?pid=1572834106552X1465002)----12'

[小海域拓展无限大海域](https://www.bilibili.com/video/av40309512)----7'

[布尔运算说明](https://www.bilibili.com/video/av67141329)----1'06''

[物体中心点$CEX,$CEY,$CEZ](https://www.bilibili.com/video/av67240826)----15'10''

[读取物体的属性及属性值](https://zhuanlan.zhihu.com/p/79783942)


[缝合点线](https://www.bilibili.com/video/av71723682)----1'

[破面模型封口](https://www.bilibili.com/video/av16210606?p=6)----1'10''

[houdini粒子输出速度通道方法](http://blog.sina.com.cn/s/blog_809e17170102w3vl.html)--[houdini怎么渲染出流体的速度通道](https://blog.csdn.net/liujiufu/article/details/82146206)

# 分类

### 渲染

* 1.提高light的sampling quality能有效减少noise

* 2.提高mantra的pixelsamples的值能有效减少noise。

* 3.提高mantra的min ray samples能提高ray trace的采样值。

* 4.mantra的dicing的shading quality multipler的值是raytrace渲染引擎发射更多的raytrace光线来采样多边形。如果使用micropolgon算法，这个值就是多边形渲染的更加细小。提高这个值，渲染精度就越高，时间越长。
