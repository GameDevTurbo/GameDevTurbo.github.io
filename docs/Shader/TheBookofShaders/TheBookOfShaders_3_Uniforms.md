# Uniforms

##  **什么是 Uniforms**

我们能从 CPU 给每个线程输入数据

因为显卡的架构，所有线程的输入值必须**统一**（uniform），而且必须设为**只读**。

也就是说，每条线程接收相同的数据，并且是不可改变的数据。



这些输入值叫做 `uniform` （统一值）。

它们的数据类型通常为：

`float`, `vec2`, `vec3`, `vec4`, `mat2`, `mat3`, `mat4`, `sampler2D` and `samplerCube`。

uniform 值需要数值类型前后一致。

且在 shader 的开头，在设定精度之后，就对其进行定义。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution; // 画布尺寸（宽，高）
uniform vec2 u_mouse;      // 鼠标位置（在屏幕上哪个像素）
uniform float u_time;     // 时间（加载后的秒数）
```

虽然这些 uniforms 的名字千奇百怪，但是在这一系列的例子中我一直有用到：

- `u_time` （时间）
- `u_resolution` （画布尺寸）
- `u_mouse` （鼠标位置）

按业界传统应在 uniform 值的名字前加 `u_` ，这样一看即知是 uniform。

尽管如此你也还会见到各种各样的名字。比如[ShaderToy.com](https://www.shadertoy.com/)就用了如下的名字：

```glsl
uniform vec3 iResolution;   // 视口分辨率（以像素计）
uniform vec4 iMouse;        // 鼠标坐标 xy： 当前位置, zw： 点击位置
uniform float iTime;        // shader 运行时间（以秒计）
```



在下面的代码中我们使用 `u_time` 加上一个 sin 函数，来展示图中红色的动态变化。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform float u_time;

void main() {
	gl_FragColor = vec4(abs(sin(u_time)),0.0,0.0,1.0);
}

```



GLSL 还有更多惊喜。

GPU 的硬件加速支持我们使用**角度**，**三角函数**和**指数函数**。

这里有一些这些函数的介绍：[`sin()`](https://thebookofshaders.com/glossary/?search=sin), [`cos()`](https://thebookofshaders.com/glossary/?search=cos), [`tan()`](https://thebookofshaders.com/glossary/?search=tan), [`asin()`](https://thebookofshaders.com/glossary/?search=asin), [`acos()`](https://thebookofshaders.com/glossary/?search=acos), [`atan()`](https://thebookofshaders.com/glossary/?search=atan), [`pow()`](https://thebookofshaders.com/glossary/?search=pow), [`exp()`](https://thebookofshaders.com/glossary/?search=exp), [`log()`](https://thebookofshaders.com/glossary/?search=log), [`sqrt()`](https://thebookofshaders.com/glossary/?search=sqrt), [`abs()`](https://thebookofshaders.com/glossary/?search=abs), [`sign()`](https://thebookofshaders.com/glossary/?search=sign), [`floor()`](https://thebookofshaders.com/glossary/?search=floor), [`ceil()`](https://thebookofshaders.com/glossary/?search=ceil), [`fract()`](https://thebookofshaders.com/glossary/?search=fract), [`mod()`](https://thebookofshaders.com/glossary/?search=mod), [`min()`](https://thebookofshaders.com/glossary/?search=min), [`max()`](https://thebookofshaders.com/glossary/?search=max) 和 [`clamp()`](https://thebookofshaders.com/glossary/?search=clamp)。



```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    st.x *= u_resolution.x/u_resolution.y;

    vec3 color = vec3(0.);
    color = vec3(st.x,st.y,abs(sin(u_time)));

    gl_FragColor = vec4(color,1.0);
}
```

---





## **gl_FragCoord**

就像 GLSL 有个默认输出值 `gl_FragColor:vec4` 一样，有一个默认输入值`gl_FragCoord:vec4` 

`gl_FragCoord`存储了活动线程正在处理的**像素**或**屏幕碎片**的坐标。

有了它我们就知道了屏幕上的哪一个线程正在运转。



#### **varying**

为什么我们不叫 `gl_FragCoord` uniform （统一值）呢？因为每个像素的坐标都不同，所以我们把它叫做 **varying**（变化值）。



#### **归一化**

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main() {
	vec2 st = gl_FragCoord.xy/u_resolution;
	gl_FragColor = vec4(st.x,st.y,0.0,1.0);
}
```

上述代码中我们用 `gl_FragCoord.xy` 除以 `u_resolution`，对坐标进行了**归一化**。

这样做是为了使所有的值落在 `0.0` 到 `1.0` 之间，这样就可以轻松把 X 或 Y 的值映射到红色或者绿色通道。



##### 现在我们来检验一下我们对上面代码的理解程度。

- 你明白 `(0.0,0.0)` 坐标在画布上的哪里吗？
- 那 `(1.0,0.0)`, `(0.0,1.0)`, `(0.5,0.5)` 和 `(1.0,1.0)` 呢？
- 你知道如何用**未**规范化（normalized）的 `u_mouse` 吗？你可以用它来移动颜色吗？
- 你可以用 `u_time` 和 `u_mouse` 来改变颜色的图案吗？不妨琢磨一些有趣的途径。

我的测试方案如下：

1. 先设定好gl_FragColor，比如我要查询（1,1）坐标，则值为vec4(1,1,0,1.0); 并记录当前颜色

2. 鼠标值归一化变化到记录的颜色，就可以知道画布的具体坐标。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main() {
	vec2 st = gl_FragCoord.xy/u_resolution;
	vec2 mt = u_mouse.xy/u_resolution;
	gl_FragColor = vec4(mt.x,mt.y,0,1.0);
	// gl_FragColor = vec4(1,0,0,1.0);
}
```







