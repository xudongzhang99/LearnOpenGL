# 你好三角形
[toc]
## 理论
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
![Graphics Pipeline Shader](https://github.com/xudongzhang99/LearnOpenGL/blob/master/OpenGLDemo/Triangle/Images/2_pipeline_shader.png?raw=true)




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


## 实战
### 着色器
#### 顶点着色器
```glsl
#version 330 core
layout (location = 0) in vec3 aPos;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```
#### 片段着色器
```glsl
#version 330 core
out vec4 FragColor;

void main()
{
    FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
} 
```
#### 编译链接着色器

```c++
const char *vertexShaderSource = "#version 330 core\n"
    "layout (location = 0) in vec3 aPos;\n"
    "void main()\n"
    "{\n"
    "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
    "}\0";
const char *fragmentShaderSource = "#version 330 core\n"
    "out vec4 FragColor;\n"
    "void main()\n"
    "{\n"
    "   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
    "}\n\0";
 // vertex shader
 int vertexShader = glCreateShader(GL_VERTEX_SHADER);
 glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
 glCompileShader(vertexShader);
 // check for shader compile errors
 int success;
 char infoLog[512];
 glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
 if (!success)
 {
     glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
     std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
 }
 // fragment shader
 int fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
 glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
 glCompileShader(fragmentShader);
 // check for shader compile errors
 glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
 if (!success)
 {
     glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);
     std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;
 }
 // link shaders
 int shaderProgram = glCreateProgram();
 glAttachShader(shaderProgram, vertexShader);
 glAttachShader(shaderProgram, fragmentShader);
 glLinkProgram(shaderProgram);
 // check for linking errors
 glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
 if (!success) {
     glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
     std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << infoLog << std::endl;
 }
 glDeleteShader(vertexShader);
 glDeleteShader(fragmentShader);
```

### 三维坐标系
```
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
    -0.5f,  0.5f, 0.0f
};
```

![3DCoordinate](https://github.com/xudongzhang99/LearnOpenGL/blob/master/OpenGLDemo/Triangle/Images/2_coordinate_system.png?raw=true)


### 顶点数据
#### 顶点缓冲对象：Vertex Buffer Object，VBO
  顶点缓冲对象VBO是在显卡存储空间中开辟出的一块内存缓存区，用于存储顶点的各类属性信息，如顶点坐标，顶点法向量，顶点颜色数据等。在渲染时，可以直接从VBO中取出顶点的各类属性数据，由于VBO在显存而不是在内存中，不需要从CPU传输数据，处理效率更高。
  可以理解为VBO就是显存中的一个存储区域，可以保持大量的顶点属性信息。并且可以开辟很多个VBO，每个VBO在OpenGL中有它的唯一标识ID，这个ID对应着具体的VBO的显存地址，通过这个ID可以对特定的VBO内的数据进行存取操作。

##### VBO的创建以及配置
1. 创建VBO的第一步需要开辟（声明/获得）显存空间并分配VBO的ID：
```C++
unsigned int VBO;
glGenBuffers(1, &VBO);
```
2. 创建的VBO可用来保存不同类型的顶点数据,创建之后需要通过分配的ID绑定（bind）一下制定的VBO。
对于同一类型的顶点数据一次只能绑定一个VBO。
绑定操作通过glBindBuffer来实现，第一个参数指定绑定的数据类型，可以是`GL_ARRAY_BUFFER`, `GL_ELEMENT_ARRAY_BUFFER`, `GL_PIXEL_PACK_BUFFER`,`GL_PIXEL_UNPACK_BUFFER`中的一个。
```c++
glBindBuffer(GL_ARRAY_BUFFER, VBO);
```
3. 调用`glBufferData`把用户定义的数据传输到当前绑定的显存缓冲区中。
 
```c++
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```
4. 顶点数据传入GPU之后，还需要通知OpenGL如何解释这些顶点数据，这个工作由函数`glVertexAttribPointer`完成：
![vertex_attribute_pointer](https://github.com/xudongzhang99/LearnOpenGL/blob/master/OpenGLDemo/Triangle/Images/vertex_attribute_pointer.png?raw=true)
```c++
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
//第一个参数指定顶点属性位置，与顶点着色器中layout(location=0)对应。
//第二个参数指定顶点属性大小。
//第三个参数指定数据类型。
//第四个参数定义是否希望数据被标准化。
//第五个参数是步长（Stride），指定在连续的顶点属性之间的间隔。
//第六个参数表示我们的位置数据在缓冲区起始位置的偏移量。
```
5. 顶点属性glVertexAttribPointer默认是关闭的，使用时要以顶点属性位置值为参数调用glEnableVertexAttribArray开启。
```c
 glEnableVertexAttribArray(0);
```

#### 顶点数组对象：Vertex Array Object，VAO
VBO保存了一个模型的顶点属性信息，每次绘制模型之前需要绑定顶点的所有信息，当数据量很大时，重复这样的动作变得非常麻烦。VAO可以把这些所有的配置都存储在一个对象中，每次绘制模型时，只需要绑定这个VAO对象就可以了。
![vertex Array Object](https://github.com/xudongzhang99/LearnOpenGL/blob/master/OpenGLDemo/Triangle/Images/vertex_array_objects.png?raw=true)
**VAO是一个保存了所有顶点数据属性的状态结合，它存储了顶点数据的格式以及顶点数据所需的VBO对象的引用。**
VAO本身并没有存储顶点的相关属性数据，这些信息是存储在VBO中的，VAO相当于是对很多个VBO的引用，把一些VBO组合在一起作为一个对象统一管理。
生成一个VAO对象并绑定：

```c++
unsigned int VBO, VAO;
glGenVertexArrays(1, &VAO);
glBindVertexArray(VAO);
//VBO的相关操作
```
#### 索引缓冲对象：Element Buffer Object，EBO或Index Buffer Object，IBO
索引缓冲对象EBO相当于OpenGL中的顶点数组的概念，是为了解决同一个顶点多次重复调用的问题，可以减少内存空间浪费，提高执行效率。当需要使用重复的顶点时，通过顶点的位置索引来调用顶点，而不是对重复的顶点信息重复记录，重复调用。
EBO中存储的内容就是顶点位置的索引indices，EBO跟VBO类似，也是在显存中的一块内存缓冲器，只不过EBO保存的是顶点的索引。
![vertex_array_objects_ebo](https://github.com/xudongzhang99/LearnOpenGL/blob/master/OpenGLDemo/Triangle/Images/vertex_array_objects_ebo.png?raw=true)
创建EBO并绑定，用glBufferData（以GL_ELEMENT_ARRAY_BUFFER为参数）把索引存储到EBO中：
```c++
unsigned int EBO;
glGenBuffers(1, &EBO);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```

当用EBO绑定顶点索引的方式绘制模型时，需要使用glDrawElements而不是glDrawArrays：
```c++
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
//第一个参数指定了要绘制的模式；
//第二个参数指定要绘制的顶点个数；
//第三个参数是索引的数据类型；
//第四个参数是可选的EBO中偏移量设定。
```

### 绘制
```c++
lUseProgram(shaderProgram);
glBindVertexArray(VAO); // seeing as we only have a single VAO there's no need to bind it every time, but we'll do so to keep things a bit more organized
//glDrawArrays(GL_TRIANGLES, 0, 6); 
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```
