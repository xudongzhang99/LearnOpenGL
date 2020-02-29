# 你好三角形
[toc]
OpenGL大部分工作是关于把3D坐标转变为2D坐标，这个过程叫图形渲染渲染管线(Graphics Pipeline ). 
### Graphics Pipeline 
Graphics Pipeline： 其实就是把一堆原始数据途径一个输送管道，期间经过各种变化处理，最终出现在屏幕上。
Graphics Pipeline：的工作可以分为两部分
1. 把3D坐标转化为2D坐标，
2. 把2D坐标转变为实际的有颜色的像素。

>2D坐标和像素也是不同的，2D坐标精确表示一个点在2D空间中的位置，而2D像素是这个点的近似值，2D像素受到你的屏幕/窗口分辨率的限制。
### Graphics Pipeline 步骤
Graphics Pipeline 可以划分为下面的几个阶段，每个阶段接受上一个阶段的输出作为输入（这些阶段都是高度专门化的，很容易并行。）
#### Shader(着色器)
着色器(Shader)： GPU的小处理核心为每一个（Graphics Pipeline）阶段运行自己的小程序。有些着色器运训开发者自己配置（使用自己的着色器来替换默认的实现），以达到更加细致的控制。OpenGL着色器是用OpenGL着色器语言(OpenGL Shading Language, GLSL)编写的。
>注意蓝色部分代表的是我们可以注入自定义的着色器的部分。
![Graphics Pipeline Shader](https://github.com/xudongzhang99/LearnOpenGL/blob/master/Images/2_pipeline_shader.png?raw=true)




```sequence
Note left of Vertex Data:  Vertex Data:\n数组的形式传递3D坐标\n   由Vertex Attribute表示 
Vertex Data-->Vertex Shader(必须自定义): Vertex Data
Vertex Shader(必须自定义)-->Primitive Assembly: 另外一种3D坐标
Primitive Assembly-->Geometry Shader(可自定义):指定的图元形状
Geometry Shader(可自定义)-->Rasterization:其他的形状
Rasterization-->Fragment Shader(必须自定义):屏幕上相应的像素 Fragment
Fragment Shader(必须自定义)-->Tests And Belending:像素最终颜色
Tests And Belending-->Screen: 最终像素

```

##### Vertex Data：
顶点数据(Vertex Data)；顶点数据是一系列顶点的集合。
一个顶点(Vertex)是一个3D坐标的数据的集合。

>而顶点数据是用顶点属性(Vertex Attribute)表示的，它可以包含任何我们想用的数据。
当我们谈论一个“位置”的时候，它代表在一个“空间”中所处地点的这个特殊属性；同时“空间”代表着任何一种坐标系，比如x、y、z三维坐标系，x、y二维坐标系，或者一条直线上的x和y的线性关系，只不过二维坐标系是一个扁扁的平面空间，而一条直线是一个很瘦的长长的空间。
为了让OpenGL知道我们的坐标和颜色值构成的到底是什么，OpenGL需要你去指定这些数据所表示的渲染类型。我们是希望把这些数据渲染成一系列的点？一系列的三角形？还是仅仅是一个长长的线？做出的这些提示叫做图元(Primitive)，任何一个绘制指令的调用都将把图元传递给OpenGL。这是其中的几个：GL_POINTS、GL_TRIANGLES、GL_LINE_STRIP。
##### Vertex Shader(顶点着色器)
把输入的D坐标转为另一种3D坐标，顶点着色器允许我们对顶点属性进行一些基本处理。
##### Primitive Assembly(图元\形状 装配) 
把顶点着色器输出的所有顶点作为输入， 并所有的点装配成指定图元的形状。
##### Geometry Shader(几何着色器)
把图元形式的一系列顶点的集合作为输入，它可以通过产生新顶点构造出新的（或是其它的）图元来生成其他形状。
##### Rasterization(光栅化)
把图元映射为最终屏幕上相应的像素，生成供片段着色器(Fragment Shader)使用的片段(Fragment)。
在片段着色器运行之前会执行裁切(Clipping)。裁切会丢弃超出你的视图以外的所有像素，用来提升执行效率。
>OpenGL中的一个片段是OpenGL渲染一个像素所需的所有数据。
##### Fragment Shader(片段着色器)
主要目的是计算一个像素的最终颜色，这也是所有OpenGL高级效果产生的地方。通常，片段着色器包含3D场景的数据（比如光照、阴影、光的颜色等等），这些数据可以被用来计算最终像素的颜色。
##### Tests And Belending(测试与混合)
这个阶段检测片段的对应的深度（和模板(Stencil)）值（后面会讲），用它们来判断这个像素是其它物体的前面还是后面，决定是否应该丢弃。这个阶段也会检查alpha值（alpha值定义了一个物体的透明度）并对物体进行混合(Blend)。所以，即使在片段着色器中计算出来了一个像素输出的颜色，在渲染多个三角形的时候最后的像素颜色也可能完全不同。


大多数场合，我们只需要配置顶点和片段着色器就行了。几何着色器是可选的，通常使用它默认的着色器就行了。
在现代OpenGL中，我们必须定义至少一个顶点着色器和一个片段着色器（因为GPU中没有默认的顶点/片段着色器。

![三维坐标系](https://github.com/xudongzhang99/LearnOpenGL/blob/master/Images/2_coordinate_system.png?raw=true)