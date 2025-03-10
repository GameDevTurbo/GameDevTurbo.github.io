# 什么是 Fragment Shader(片段着色器)？



## 为什么 shaders 运行特别快？

#### **串行处理**

想象你的 CPU 是一个大的工业管道，然后每一个任务都是通过这个管道的某些东西 —— 就像一个生产流水线那样。

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/00.jpeg" alt="CPU" style="zoom:50%;" />



#### **线程**

现代计算机通常有一组四个处理器，就像这个管道一样运行，一个接一个地处理这些任务，从而使计算机流畅运行。每个管道通常被称为**线程**。



#### **并行处理**

屏幕上的每个像素都代表一个最简单的任务。单独来看完成任何一个像素的任务对 CPU 来说都很容易，那么问题来了，屏幕上的每一个像素都需要解决这样的小任务！也就是说，哪怕是对于一个老式的屏幕（分辨率 800x600）来说，都需要每帧处理480000个像素，即每秒进行14400000次计算！是的，这对于微处理器就是大问题了！而对于一个现代的 2800x1800 视网膜屏，每秒运行60帧，就需要每秒进行311040000次计算。

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/03.jpeg" alt="img" style="zoom:50%;" />



这个时候，并行处理就是最好的解决方案。比起用三五个强大的微处理器（或者说“管道”）来处理这些信息，用一大堆小的微处理器来并行计算，就要好得多。这就是图形处理器（GPU : Graphic Processor Unit)的来由。



<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/04.jpeg" alt="GPU" style="zoom:50%;" />



设想一堆小型微处理器排成一个平面的画面，假设每个像素的数据是乒乓球。14400000个乒乓球可以在一秒内阻塞几乎任何管道。但是一面800x600的管道墙，每秒接收30波480000个像素的信息就可以流畅完成。这在更高的分辨率下也是成立的 —— 并行的处理器越多，可以处理的数据流就越大。

另一个 GPU 的魔法是特殊数学函数可通过硬件加速。非常复杂的数学操作可以直接被微芯片解决，而无须通过软件。这就表示可以有更快的三角和矩阵运算 —— 和电流一样快。



## GLSL是什么？

GLSL 代表 openGL Shading Language，openGL 着色语言，这是你在接下来章节看到的程序所遵循的具体标准。根据硬件和操作系统的不同，还有其他的着色器（shaders)。这里我们将依照[Khronos Group](https://www.khronos.org/opengl/)的规则来执行。了解 OpenGL 的历史将有助于你理解大多数奇怪的约定，所以建议不妨阅读[openglbook.com/chapter-0-preface-what-is-opengl.html](http://openglbook.com/chapter-0-preface-what-is-opengl.html)。