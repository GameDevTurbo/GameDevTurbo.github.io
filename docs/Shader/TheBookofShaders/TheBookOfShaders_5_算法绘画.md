# 算法绘画



## 造型函数



```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

// Plot a line on Y using a value between 0.0-1.0
float plot(vec2 st) {    
    return smoothstep(0.02, 0.0, abs(st.y - st.x));
}

void main() {
	vec2 st = gl_FragCoord.xy/u_resolution;

    float y = st.x;

    vec3 color = vec3(y);

    // Plot a line
    float pct = plot(st);
    color = (1.0-pct)*color+pct*vec3(0.0,1.0,0.0);

	gl_FragColor = vec4(color,1.0);
}
```

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/下载 (2).png" alt="下载 (2)" style="zoom:50%;" />

你可以通过 `float` 快速赋值  `r,g,b`  三个通道

同样，你可以用 `vec3` 快速赋值 `vec4` 的  `r,g,b ` 三个通道



```glsl
 float y = st.x;
 vec3 color = vec3(y);
 vec3 color2 = vec3(y,y,y);
 // color = color2

vec4 fColor = vec4(color,1.0);
vec4 fColor2 = vec4(color.x,color.y,color.z,1.0);
// fColor = fColor2
gl_FragColor = fColor;
```







```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265359

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

float plot(vec2 st, float pct){
  return  smoothstep( pct-0.02, pct, st.y) -
          smoothstep( pct, pct+0.02, st.y);
}

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution;

    float y = pow(st.x,5.0);

    vec3 color = vec3(y);

    float pct = plot(st,y);
    color = (1.0-pct)*color+pct*vec3(0.0,1.0,0.0);

    gl_FragColor = vec4(color,1.0);
}
```

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/%E4%B8%8B%E8%BD%BD%20(1).png" alt="下载 (1)" style="zoom:50%;" />

[`pow()`](https://thebookofshaders.com/glossary/?search=pow) （求x的y次幂）是 GLSL 的一个原生函数，GLSL 有很多原生函数。大多数原生函数都是硬件加速的。

当你用 Pi 来玩的时候有些方程会变得更有趣。在第 5 行我定义了一个宏，使得每当程序调用 `PI` 的时候就用 `3.14159265359` 来替换它。



#### Step 和 Smoothstep



###### Step

[`step()`](https://thebookofshaders.com/glossary/?search=step) 插值函数需要输入两个参数。

第一个是极限或阈值，第二个是我们想要检测或通过的值。

对任何小于阈值的值，返回 `0.0`，大于阈值，则返回 `1.0`。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265359

uniform vec2 u_resolution;
uniform float u_time;

float plot(vec2 st, float pct){
  return  smoothstep( pct-0.02, pct, st.y) -
          smoothstep( pct, pct+0.02, st.y);
}

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution;

    // Step will return 0.0 unless the value is over 0.5,
    // in that case it will return 1.0
    float y = step(1.396,st.x);

    vec3 color = vec3(y);

    float pct = plot(st,y);
    color = (1.0-pct)*color+pct*vec3(0.0,1.0,0.0);

    gl_FragColor = vec4(color,1.0);
}
```

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/%E4%B8%8B%E8%BD%BD.png" alt="下载" style="zoom:50%;" />



#####  Smoothstep

另一个 GLSL 的特殊函数是 [`smoothstep()`](https://thebookofshaders.com/glossary/?search=smoothstep)。

当给定一个范围的上下限和一个数值，这个函数会在已有的范围内给出插值。

前两个参数规定转换的开始和结束点，第三个是给出一个值用来插值。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265359

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

float plot(vec2 st, float pct){
  return  smoothstep( pct-0.02, pct, st.y) -
          smoothstep( pct, pct+0.02, st.y);
}

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution;

    // Smooth interpolation between 0.1 and 0.9
    float y = smoothstep(0.1,0.9,st.x);

    vec3 color = vec3(y);

    float pct = plot(st,y);
    color = (1.0-pct)*color+pct*vec3(0.0,1.0,0.0);

    gl_FragColor = vec4(color,1.0);
}
```

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/image-20220521145106267.png" alt="image-20220521145106267" style="zoom:50%;" />



```glsl
  float y = smoothstep(0.2,0.5,st.x) - smoothstep(0.5,0.8,st.x);
```

> 用它替换上面的第 20 行，把它想成是一个垂直切割。

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/image-20220521145012575.png" alt="image-20220521145012575" style="zoom:50%;" />

> 背景看起来很像一条线，不是吗？



### 正弦和余弦函数

> 当你想用数学来制造动效，形态或去混合数值，sin 和 cos 就是你的最佳伙伴。

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/sincos.gif)



简单来说，当我们给出一个角度（这里采用弧度制），它就会返回半径为一的圆上一个点的 x 坐标（[cos](https://thebookofshaders.com/glossary/?search=cos)）和 y 坐标（[sin](https://thebookofshaders.com/glossary/?search=sin)）。

正因为 sin 和 cos 返回的是归一化的值（即值域在 -1 和 1 之间），且如此流畅，这就使得它成为一个极其强大的工具。



##### 动动手？（[use this](https://thebookofshaders.com/05/?lan=ch)）

- 在 `sin` 里让 x 加上时间（`u_time`）。让sin 曲线随 x 轴**动起来**。
- 在 `sin` 里用 `PI` 乘以 x。注意 sin 曲线上下波动的两部分如何**收缩**了，现在 sin 曲线每两个整数循环一次。
- 在 `sin` 里用时间（ `u_time`)乘以 x。观察各阶段的循环如何变得越来越**频繁**。注意 u_time 可能已经变得非常大，使得图像难以辨认。
- 给 [`sin(x)`](https://thebookofshaders.com/glossary/?search=sin)（注意不是 sin 里的 x）加 1.0。观察曲线是如何向上**移动**的，现在值域变成了 0.0 到 2.0。
- 给 [`sin(x)`](https://thebookofshaders.com/glossary/?search=sin) 乘以 2.0。观察曲线大小如何**增大**两倍。
- 计算 `sin(x)` 的绝对值（[`abs()`](https://thebookofshaders.com/glossary/?search=abs)）。现在它看起来就像一个**弹力球**的轨迹。
- 只选取 `sin(x)` 的小数部分（[`fract()`](https://thebookofshaders.com/glossary/?search=fract)）。
- 使用向正无穷取整（[`ceil()`](https://thebookofshaders.com/glossary/?search=ceil)）和向负无穷取整（[`floor()`](https://thebookofshaders.com/glossary/?search=floor)），使得 sin 曲线变成只有 1 和 -1 的电子波。



## 其他有用的函数



```
y = mod(x,0.556); // 返回 x 对 0.5 取模的值
//y = fract(x); // 仅仅返回数的小数部分
//y = ceil(x);  // 向正无穷取整
//y = floor(x); // 向负无穷取整
//y = sign(x);  // 提取 x 的正负号
//y = abs(x);   // 返回 x 的绝对值
//y = clamp(x,0.0,1.0); // 把 x 的值限制在 0.0 到 1.0
//y = min(0.0,x);   // 返回 x 和 0.0 中的较小值
//y = max(0.0,x);   // 返回 x 和 0.0 中的较大值  
```





## 造型函数进阶

[Golan Levin](http://www.flong.com/) 写过关于更加复杂的造型函数的文档，非常有帮助。把它们引入 GLSL 是非常明智的选择，这将是你的代码的广阔的素材库

- [多项式造型函数（Polynomial Shaping Functions）: www.flong.com/archive/texts/code/shapers_poly](http://www.flong.com/archive/texts/code/shapers_poly/)
- [指数造型函数（Exponential Shaping Functions）: www.flong.com/archive/texts/code/shapers_exp](http://www.flong.com/archive/texts/code/shapers_exp/)
- [圆与椭圆的造型函数（Circular & Elliptical Shaping Functions）: www.flong.com/archive/texts/code/shapers_circ](http://www.flong.com/archive/texts/code/shapers_circ/)
- [贝塞尔和其他参数化造型函数（Bezier and Other Parametric Shaping Functions）: www.flong.com/archive/texts/code/shapers_bez](http://www.flong.com/archive/texts/code/shapers_bez/)



[Iñigo Quiles](http://www.iquilezles.org/) 收集了一套[有用的函数](http://www.iquilezles.org/www/articles/functions/functions.htm)。在看过[这篇文章](http://www.iquilezles.org/www/articles/functions/functions.htm)后，看看下列函数转换到 GLSL 的样子。注意那些细小的改变，比如给浮点数（float)加小数点“.”，给“C 系函数”换成它们在 GLSL 里的名字，比如不是用 `powf()` 而是用 `pow()`：

- [Impulse](https://thebookofshaders.com/edit.php#05/impulse.frag)
- [Cubic Pulse](https://thebookofshaders.com/edit.php#05/cubicpulse.frag)
- [Exponential Step](https://thebookofshaders.com/edit.php#05/expstep.frag)
- [Parabola](https://thebookofshaders.com/edit.php#05/parabola.frag)
- [Power Curve](https://thebookofshaders.com/edit.php#05/pcurve.frag)



来看看 [Kynd](http://www.kynd.info/log/) 帮大家制作的公式表。

看他如何结合各种函数及它们的属性，始终控制值的范围在 0.0 到 1.0。

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/kynd.png" alt="kynd"  />



#### 填充你的工具箱



- Grapher：如果你是用 MacOS 系统，用 spotlight 搜 `grapher` 就会看到这个超级方便的工具了。



![OS X Grapher (2004)](https://thebookofshaders.com/05/grapher.png)

OS X Grapher (2004)



- [GraphToy](http://www.iquilezles.org/apps/graphtoy/)：仍然是 [Iñigo Quilez](http://www.iquilezles.org/) 为大家做的工具，用于在 WebGL 中可视化 GLSL 函数。



![Iñigo Quilez - GraphToy (2010)](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/graphtoy.png)

Iñigo Quilez - GraphToy (2010)



- [Shadershop](http://tobyschachman.com/Shadershop/)：这个超级棒的工具是 [Toby Schachman](http://tobyschachman.com/) 的作品。它会以一种极其视觉化和直观的方式教你如何建造复杂的函数。



![Toby Schachman - Shadershop (2014)](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/shadershop.png)

Toby Schachman - Shadershop (2014)

