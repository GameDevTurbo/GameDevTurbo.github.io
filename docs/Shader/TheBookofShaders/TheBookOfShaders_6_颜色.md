# 颜色



#### 颜色定义

若你熟悉面向对象的编程范式（或者说编程思维模式），你一定注意到我们以一种类C的 `struct`的方式访问向量数据的内部分量。

```glsl
vec3 red = vec3(1.0,0.0,0.0);
red.x = 1.0;
red.y = 0.0;
red.z = 0.0;
```

以x,y,z定义颜色是不是有些奇怪？

正因如此，我们有其他方法访问这些变量——以不同的名字。

`.x`, `.y`, `.z`也可以被写作`.r`, `.g`, `.b` 和 `.s`, `.t`, `.p`。（`.s`, `.t`, `.p`通常被用做后面章节提到的贴图空间坐标）你也可以通过使用索引位置`[0]`, `[1]` 和 `[2]`来访问向量.



下面的代码展示了所有访问相同数据的方式：

```glsl
vec4 vector;
vector[0] = vector.r = vector.x = vector.s;
vector[1] = vector.g = vector.y = vector.t;
vector[2] = vector.b = vector.z = vector.p;
vector[3] = vector.a = vector.w = vector.q;
```



GLSL中向量类型的另一大特点是可以用你需要的任意顺序简单地投射和混合（变量）值。这种能力被（形象地）称为：**鸡尾酒(*swizzle*)**。

```glsl
vec3 yellow, magenta, green;

// Making Yellow
yellow.rg = vec2(1.0);  // Assigning 1. to red and green channels
yellow[2] = 0.0;        // Assigning 0. to blue channel

// Making Magenta
magenta = yellow.rbg;   // Assign the channels with green and blue swapped

// Making Green
green.rgb = yellow.bgb; // Assign the blue channel of Yellow (0) to red and blue channels
```





#### 混合颜色

在GLSL中，有个十分有用的函数：[`mix()`](https://thebookofshaders.com/glossary/?search=mix)，这个函数让你以百分比混合两个值。

猜下百分比的取值范围？没错，0到1！完美！

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/mix-f.jpg)



下面展示了我们如果是用随时间变化的sin绝对值来混合 `colorA` 和 `colorB`。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform float u_time;

vec3 colorA = vec3(0.149,0.141,0.912);
vec3 colorB = vec3(1.000,0.833,0.224);

void main() {
    vec3 color = vec3(0.0);

    float pct = abs(sin(u_time));

    // Mix uses pct (a value from 0-1) to
    // mix the two colors
    color = mix(colorA, colorB, pct);

    gl_FragColor = vec4(color,1.0);
}
```



#### 关于 mix

mix(vec3 color1, vec3 color2,float mixValue)

mix 通过 mixValue 混合2个颜色值

假定 mixValue = 0.0, 那么颜色会显现出  color1 的颜色

假定 mixValue = 1.0, 那么颜色会显现出  color2 的颜色

mixValue 介于 0.0 - 1.0 之间



#### 玩玩渐变

[`mix()`](https://thebookofshaders.com/glossary/?search=mix) 函数有更多的用处。

我们可以输入两个互相匹配的变量类型而不仅仅是单独的 `float` 变量，在我们这个例子中用的是 `vec3`。

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/mix-vec.jpg)



正如前面一个例子，我们用一条线来可视化根据单位化x坐标的过渡。现在所有通道都按照同样的线性变换过渡。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265359

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

vec3 colorA = vec3(0.149,0.141,0.912);
vec3 colorB = vec3(1.000,0.833,0.224);

float plot (vec2 st, float pct){
  return  smoothstep( pct-0.01, pct, st.y) -
          smoothstep( pct, pct+0.01, st.y);
}

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(0.0);

    vec3 pct = vec3(st.x);

    // pct.r = smoothstep(0.0,1.0, st.x);
    // pct.g = sin(st.x*PI);
    // pct.b = pow(st.x,0.5);

    color = mix(colorA, colorB, pct);

    // Plot transition lines for each channel
    color = mix(color,vec3(1.0,0.0,0.0),plot(st,pct.r));
    color = mix(color,vec3(0.0,1.0,0.0),plot(st,pct.g));
    color = mix(color,vec3(0.0,0.0,1.0),plot(st,pct.b));

    gl_FragColor = vec4(color,1.0);
}
```



#### HSB

> HSV即色相、飽和度、明度（英語：**Hue**, Saturation, Value），又稱HSB，其中B即英語：Brightness。
>
> 色调（H, Hue），饱和度（S,Saturation），明度（V, Value）。

我们不能脱离色彩空间来谈论颜色。

正如你所知，除了rgb值，有其他不同的方法去描述定义颜色。

[HSB](http://en.wikipedia.org/wiki/HSL_and_HSV) 代表色相，饱和度和亮度（或称为值）。

<img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/20180520172109456" alt="img" style="zoom: 67%;" /><img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/20180520172120828" alt="img" style="zoom: 67%;" />

将x坐标（位置）映射到Hue值并将y坐标映射到明度，我们就得到了五彩的可见光光谱。这样的色彩空间分布实现起来非常方便，比起RGB，用HSB来拾取颜色更直观。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform float u_time;

vec3 rgb2hsb( in vec3 c ){
    vec4 K = vec4(0.0, -1.0 / 3.0, 2.0 / 3.0, -1.0);
    vec4 p = mix(vec4(c.bg, K.wz),
                 vec4(c.gb, K.xy),
                 step(c.b, c.g));
    vec4 q = mix(vec4(p.xyw, c.r),
                 vec4(c.r, p.yzx),
                 step(p.x, c.r));
    float d = q.x - min(q.w, q.y);
    float e = 1.0e-10;
    return vec3(abs(q.z + (q.w - q.y) / (6.0 * d + e)),
                d / (q.x + e),
                q.x);
}

//  Function from Iñigo Quiles
//  https://www.shadertoy.com/view/MsS3Wc
vec3 hsb2rgb( in vec3 c ){
    vec3 rgb = clamp(abs(mod(c.x*6.0+vec3(0.0,4.0,2.0),
                             6.0)-3.0)-1.0,
                     0.0,
                     1.0 );
    rgb = rgb*rgb*(3.0-2.0*rgb);
    return c.z * mix(vec3(1.0), rgb, c.y);
}

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    // We map x (0.0 - 1.0) to the hue (0.0 - 1.0)
    // And the y (0.0 - 1.0) to the brightness
    color = hsb2rgb(vec3(st.x,1.0,st.y));

    gl_FragColor = vec4(color,1.0);
}
```



#### 极坐标下的HSB

HSB原本是在极坐标下产生的（以半径和角度定义）而并非在笛卡尔坐标系（基于xy定义）下。

将HSB映射到极坐标我们需要取得角度和到像素屏中点的距离。

由此我们运用 [`length()`](https://thebookofshaders.com/glossary/?search=length) 函数和 [`atan(y,x)`](https://thebookofshaders.com/glossary/?search=atan) 函数（在GLSL中通常用atan（y,x））。



**注意**：如果你想了解，除length（）以外的诸多几何函数，例如：[`distance()`](https://thebookofshaders.com/glossary/?search=distance), [`dot()`](https://thebookofshaders.com/glossary/?search=dot), [`cross`](https://thebookofshaders.com/glossary/?search=cross), [`normalize()`](https://thebookofshaders.com/glossary/?search=normalize), [`faceforward()`](https://thebookofshaders.com/glossary/?search=faceforward), [`reflect()`](https://thebookofshaders.com/glossary/?search=reflect) 和 [`refract()`](https://thebookofshaders.com/glossary/?search=refract)。 GLSL也有与向量相关的函数：[`lessThan()`](https://thebookofshaders.com/glossary/?search=lessThan), [`lessThanEqual()`](https://thebookofshaders.com/glossary/?search=lessThanEqual), [`greaterThan()`](https://thebookofshaders.com/glossary/?search=greaterThan), [`greaterThanEqual()`](https://thebookofshaders.com/glossary/?search=greaterThanEqual), [`equal()`](https://thebookofshaders.com/glossary/?search=equal) and [`notEqual()`](https://thebookofshaders.com/glossary/?search=notEqual)。



一旦我们得到角度和长度，我们需要单位化这些值：0.0到1.0。

在27行，[`atan(y,x)`](https://thebookofshaders.com/glossary/?search=atan) 会返回一个介于-PI到PI的弧度值（-3.14 to 3.14），所以我们要将这个返回值除以 `TWO_PI`（在code顶部定义了）来得到一个-0.5到0.5的值。这样一来，用简单的加法就可以把这个返回值最终映射到0.0到1.0。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define TWO_PI 6.28318530718

uniform vec2 u_resolution;
uniform float u_time;

//  Function from Iñigo Quiles
//  https://www.shadertoy.com/view/MsS3Wc
vec3 hsb2rgb( in vec3 c ){
    vec3 rgb = clamp(abs(mod(c.x*6.0+vec3(0.0,4.0,2.0),
                             6.0)-3.0)-1.0,
                     0.0,
                     1.0 );
    rgb = rgb*rgb*(3.0-2.0*rgb);
    return c.z * mix( vec3(1.0), rgb, c.y);
}

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    // Use polar coordinates instead of cartesian
    vec2 toCenter = vec2(0.5)-st;
    float angle = atan(toCenter.y,toCenter.x);
    float radius = length(toCenter)*2.0;

    // Map the angle (-PI to PI) to the Hue (from 0 to 1)
    // and the Saturation to the radius
    color = hsb2rgb(vec3((angle/TWO_PI)+0.5,radius,1.0));

    gl_FragColor = vec4(color,1.0);
}
```



半径会返回一个最大值0.5（因为我们计算的是到视口中心的距离，而视口中心的范围已经被映射到0.0到1.0），所以我们需要把这个值乘以二来得到一个0到1.0的映射。



##### 来挑战下下面的练习吧

- 把极坐标映射的例子改成选择色轮，就像“正忙”的鼠标图标。

- 把造型函数整合进来，来让HSB和RGB的转换中强调某些特定值并且弱化其他的。

  - <img src="https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/spectrums.jpg" alt="William Home Lizars - Red, blue and yellow spectra, with the solar spectrum (1834)" style="zoom:50%;" />

- 如果你仔细观察用来拾色的色轮（见下图），你会发现它用一种根据RYB色彩空间的色谱。例如，红色的对面应该是绿色，但在我们的例子里是青色。你能找到一种修复的方式来让它看起来和下图一样么？[提示：这是用塑形函数的好机会！]

  - ![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/colorwheel.png)

  

##### “正忙”的鼠标图标

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define TWO_PI 6.28318530718

uniform vec2 u_resolution;
uniform float u_time;

//  Function from Iñigo Quiles
//  https://www.shadertoy.com/view/MsS3Wc
vec3 hsb2rgb( in vec3 c ){
    vec3 rgb = clamp(abs(mod(c.x*6.0+vec3(0.0,4.0,2.0),
                             6.0)-3.0)-1.0,
                     0.0,
                     1.0 );
    rgb = rgb*rgb*(3.0-2.0*rgb);
    return c.z * mix( vec3(1.0), rgb, c.y);
}

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    // Use polar coordinates instead of cartesian
    vec2 toCenter = vec2(0.5)-st;
    float angle = atan(toCenter.y,toCenter.x);
    float radius = step(length(toCenter)*2.0,1.0);

    // Map the angle (-PI to PI) to the Hue (from 0 to 1)
    // and the Saturation to the radius
    color = hsb2rgb(vec3((angle/TWO_PI)+0.5 - u_time/5.0 ,radius,1.0));

    gl_FragColor = vec4(color,1.0);
}
```



#### 注意函数和变量

复习下之前例子的函数。你会注意到变量类型之前有个限定符 `in`，在这个 [*qualifier*](http://www.shaderific.com/glsl-qualifiers/#inputqualifier) (限定符)例子中它特指这个变量是只读的。

在之后的例子中我们会看到可以定义一个 `out` 或者 `inout`变量。最后这个 `inout`，再概念上类似于参照输入一个变量，这意味着我们有可能修改一个传入的变量。

```glsl
int newFunction(in vec4 aVec4,   // read-only
                out vec3 aVec3,    // write-only
                inout int aInt);   // read-write
```



