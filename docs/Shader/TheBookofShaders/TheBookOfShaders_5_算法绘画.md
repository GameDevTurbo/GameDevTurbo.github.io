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

![下载 (2)](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/下载 (2).png)

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



![下载 (1)](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/%E4%B8%8B%E8%BD%BD%20(1).png)

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

![下载](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/%E4%B8%8B%E8%BD%BD.png)



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

![image-20220521145106267](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/image-20220521145106267.png)



```glsl
  float y = smoothstep(0.2,0.5,st.x) - smoothstep(0.5,0.8,st.x);
```

> 用它替换上面的第 20 行，把它想成是一个垂直切割。背景看起来很像一条线，不是吗？

![image-20220521145012575](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/image-20220521145012575.png)



## 造型函数进阶









