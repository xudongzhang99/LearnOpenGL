# 你好三角形
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

![Graphics Pipeline Shader](https://github.com/xudongzhang99/LearnOpenGL/blob/master/Images/2_pipeline_shader.png?raw=true)

```mermaid
graph LR
A[顶点着色器-可以自定义] --> B(形状图元装配)
B --> C(几何着色器-可以自定义)
C --> D(光栅化)
D --> E(片段着色器-可以自定义)
E --> F(测试与混合)
```