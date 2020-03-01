# OpenGL
## 简介
### 什么是OpenGL
在开始这段旅程之前我们先了解一下OpenGL到底是什么。一般它被认为是一个API(Application Programming Interface, 应用程序编程接口)，包含了一系列可以操作图形、图像的函数。然而，OpenGL本身并不是一个API，它仅仅是一个由Khronos组织制定并维护的规范(Specification)。

### GLFW
个专门针对 OpenGL 的 C 语言库，提供了渲染物体所需的最低限度的接口。其允许用户创建 OpenGL 上下文，定义窗口参数以及处理用户输入，把物体渲染到屏幕所需的必要功能。（注意：OpenGL 并不规定窗口创建和管理的部分，这一部分完全交由 GLFW 来实现；还有其他类似的：GLUT 和 SDL等）

### GLEW
由于 OpenGL 只是一种 标准/规范，并且是由驱动制造上在驱动中予以实现。OpenGL 的大多数函数在编译时（compile-time）是未知状态的，需要在运行时（run-time）来请求。GLEW 的工作就是获取所需的函数的地址，并储存在函数指针中供使用。

### GLAD
因为OpenGL只是一个标准/规范，具体的实现是由驱动开发商针对特定显卡实现的。由于OpenGL驱动版本众多，它大多数函数的位置都无法在编译时确定下来，需要在运行时查询。有些库能简化此过程，其中GLAD是目前最新，也是最流行的库。
GLAD 使用了一个在线服务（能够告诉 GLAD 需要定义的 OpenGL 版本，并且根据这个版本加载所有相关的 OpenGL 函数）

```c
// 定义函数原型typedef 
void (*GL_GENBUFFERS) (GLsizei, GLuint*);
// 找到正确的函数并赋值给函数指针
GL_GENBUFFERS glGenBuffers =(GL_GENBUFFERS)wglGetProcAddress("glGenBuffers");
// 现在函数可以被正常调用了GLuint buffer;
glGenBuffers(1, &buffer);
```


## 你好三角形
OpenGL大部分工作是关于把3D坐标转变为2D坐标，这个过程叫图形渲染渲染管线(Graphics Pipeline ). 
### Graphics Pipeline 
Graphics Pipeline： 其实就是把一堆原始数据途径一个输送管道，期间经过各种变化处理，最终出现在屏幕上。
Graphics Pipeline：的工作可以分为两部分
1. 把3D坐标转化为2D坐标，
2. 把2D坐标转变为实际的有颜色的像素。

>2D坐标和像素也是不同的，2D坐标精确表示一个点在2D空间中的位置，而2D像素是这个点的近似值，2D像素受到你的屏幕/窗口分辨率的限制。
### Graphics Pipeline 步骤
Graphics Pipeline 

![Graphics Pipeline 步骤](https://learnopengl-cn.github.io/img/01/04/pipeline.png)

```mermaid
graph LR
A[顶点着色器] --> B(形状图元装配)
B --> C(几何着色器)
C --> D(光栅化)
D --> E(片段着色器)
E --> F(测试与混合)
```

# OpenGL
## 简介
### 什么是OpenGL
在开始这段旅程之前我们先了解一下OpenGL到底是什么。一般它被认为是一个API(Application Programming Interface, 应用程序编程接口)，包含了一系列可以操作图形、图像的函数。然而，OpenGL本身并不是一个API，它仅仅是一个由Khronos组织制定并维护的规范(Specification)。

### GLFW
个专门针对 OpenGL 的 C 语言库，提供了渲染物体所需的最低限度的接口。其允许用户创建 OpenGL 上下文，定义窗口参数以及处理用户输入，把物体渲染到屏幕所需的必要功能。（注意：OpenGL 并不规定窗口创建和管理的部分，这一部分完全交由 GLFW 来实现；还有其他类似的：GLUT 和 SDL等）

### GLEW
由于 OpenGL 只是一种 标准/规范，并且是由驱动制造上在驱动中予以实现。OpenGL 的大多数函数在编译时（compile-time）是未知状态的，需要在运行时（run-time）来请求。GLEW 的工作就是获取所需的函数的地址，并储存在函数指针中供使用。

### GLAD
因为OpenGL只是一个标准/规范，具体的实现是由驱动开发商针对特定显卡实现的。由于OpenGL驱动版本众多，它大多数函数的位置都无法在编译时确定下来，需要在运行时查询。有些库能简化此过程，其中GLAD是目前最新，也是最流行的库。
GLAD 使用了一个在线服务（能够告诉 GLAD 需要定义的 OpenGL 版本，并且根据这个版本加载所有相关的 OpenGL 函数）

```c++
// 定义函数原型typedef 
void (*GL_GENBUFFERS) (GLsizei, GLuint*);
// 找到正确的函数并赋值给函数指针
GL_GENBUFFERS glGenBuffers =(GL_GENBUFFERS)wglGetProcAddress("glGenBuffers");
// 现在函数可以被正常调用了GLuint buffer;
glGenBuffers(1, &buffer);
```




