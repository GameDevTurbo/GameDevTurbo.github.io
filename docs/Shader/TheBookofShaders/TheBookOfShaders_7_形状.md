# 形状



#### 长方形

想象我们有张数学课上使用的方格纸，而我们的作业是画一个正方形。纸的大小是10 x 10而正方形应该是8 x 8。你会怎么做？

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/grid_paper.jpg)

你是不是会涂满除了第一行第一列和最后一行和最后一列的所有格点？

这和着色器有什么关系？方格纸上的每个小方形格点就是一个线程（一个像素）。



我们从空间角度来判别的 if 语句伪代码开始。这个原理和我们思考方格纸的策略异曲同工。

```glsl
    if ( (X GREATER THAN 1) AND (Y GREATER THAN 1) )
        paint white
    else
        paint black
```



来试试把if语句换成step（），并用0到1代替10 x 10的范围。

```glsl
uniform vec2 u_resolution;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(0.0);

    // Each result will return 1.0 (white) or 0.0 (black).
    float left = step(0.1,st.x);   // Similar to ( X greater than 0.1 )
    float bottom = step(0.1,st.y); // Similar to ( Y greater than 0.1 )

    // The multiplication of left*bottom will be similar to the logical AND.
    color = vec3( left * bottom );

    gl_FragColor = vec4(color,1.0);
}
```

首先，明确step的作用，当后者大于前者，返回1，否则返回0；

其次，明确颜色的值，黑色为0，白色为1；



根据上述代码，可得出以下图形：

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/rect-01.jpg)



由于计算方式相近，我们可以精简代码如下：

```glsl
    vec2 borders = step(vec2(0.1),st);
    float pct = borders.x * borders.y;
```



那我们如何填充右边和上边，以此获取一个白色的正方形呢？

```glsl
// Author @patriciogv - 2015
// http://patriciogonzalezvivo.com

#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(0.0);

    // bottom-left
    vec2 bl = step(vec2(0.1),st);
    float pct = bl.x * bl.y;

    // top-right
    // vec2 tr = step(vec2(0.1),1.0-st);
    // pct *= tr.x * tr.y;

    color = vec3(pct);

    gl_FragColor = vec4(color,1.0);
}
```

取消 21~22 行的注释来看看如何转置坐标的同时重复使用 `step()` 函数。

这样二维向量 vec2(0.0,0.0) 会被变换到右上角。

这就是转置页面和重复过程的数字等价。

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/rect-02.jpg)



注意在 18 行和 22 行，所有的边（宽）都被放大了。等价于这样写：

```glsl
  vec2 bl = step(vec2(0.1),st);       // bottom-left
    vec2 tr = step(vec2(0.1),1.0-st);   // top-right
    color = vec3(bl.x * bl.y * tr.x * tr.y);
```



##### 挑战下下面的练习

- 改变长方形的比例和大小。

- 用 smoothstep() 函数代替 step() 函数，试试在相同的代码下会有什么不同。注意通过改变取值，你可以不仅可以得到模糊边界也可以由漂亮的顺滑边界。

- 应用 floor() 做个另外的案例。

- 挑个你最喜欢的做成函数，这样未来你可以调用它。并且让它灵活高效。

- 写一个只画长方形四边的函数。

- 想一下如何在一个画板上移动并放置不同的长方形？如果你做出来了，试着像[Piet Mondrian](http://en.wikipedia.org/wiki/Piet_Mondrian)一样创作以长方形和色彩的图画。

  ![Piet Mondria - Tableau (1921)](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/mondrian.jpg)



##### 绘制一个矩形边框

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265359

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){

    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(0.0);

    float right = 0.916;
    float top = 0.924;
    float line_width = 0.038;
  

    vec2 bx1 = step(vec2(0.2),st);
    vec2 bx2 = step(vec2(0.2),1.0 - st);
    
      vec2 bd1 = step(vec2(0.22),st);
    vec2 bd2 = step(vec2(0.22),1.0 - st);
    
    color = vec3(bx1.x*bx1.y*bx2.x * bx2.y) 
        - vec3(bd1.x*bd1.y*bd2.x * bd2.y);

    gl_FragColor = vec4(color,1.0);
}
```



##### 绘制一个渐变的矩形边框

```glsl
#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265359

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(0.0);

    vec2 bx1 = smoothstep(0.1,0.2,st);
    vec2 bx2 = smoothstep(0.1,0.2,1.0 - st);
    
    vec2 bb1 = smoothstep(0.15,0.35,st);    
    vec2 bb2 = smoothstep(0.15,0.35,1.0-st);
    
    color = vec3(bx1.x * bx1.y * bx2.x * bx2.y)
        - vec3(bb1.x * bb1.y * bb2.x * bb2.y);

    gl_FragColor = vec4(color,1.0);
}
```



#### 圆

让我们重新回顾一下数学课上的方格纸：我们把圆规展开到半径的长度，把一个针脚戳在圆圆心上，旋转着把圆的边界留下来。

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/compass.jpg)



将这个过程翻译给 shader 意味着纸上的每个方形格点都会隐含着问每个像素（线程）是否在圆的区域以内。

我们通过计算像素到中心的距离来实现（这个判断）。

最简单的使用[`distance()`](https://thebookofshaders.com/glossary/?search=distance)函数，它在内部计算[`length()`](https://thebookofshaders.com/glossary/?search=length)两点之间的差异（在我们的例子中是像素坐标和画布的中心）。

该`length()`函数只不过是在内部使用平方根 ([`sqrt()`](https://thebookofshaders.com/glossary/?search=sqrt))的[斜边方程的一个快捷方式。](http://en.wikipedia.org/wiki/Hypotenuse)

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/hypotenuse.png)



下面的代码包含着三个函数，毫无悬念的他们返回相同的结果。

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
	vec2 st = gl_FragCoord.xy/u_resolution;
    float pct = 0.0;

    // a. The DISTANCE from the pixel to the center
    pct = distance(st,vec2(0.5));

    // b. The LENGTH of the vector
    //    from the pixel to the center
    // vec2 toCenter = vec2(0.5)-st;
    // pct = length(toCenter);

    // c. The SQUARE ROOT of the vector
    //    from the pixel to the center
    // vec2 tC = vec2(0.5)-st;
    // pct = sqrt(tC.x*tC.x+tC.y*tC.y);

    vec3 color = vec3(pct);

	gl_FragColor = vec4( color, 1.0 );
}
```

![下载 (6)](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/%E4%B8%8B%E8%BD%BD%20(6).png)





#### Distance Field

我们可也可以从另外的角度思考上面的例子：把它当做海拔地图（等高线图）——越黑的地方意味着海拔越高。

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/distance-field.jpg)





#### 小试牛刀：

- 用[`step()`](https://thebookofshaders.com/glossary/?search=step)函数把所有大于0.5的像素点变成白色，并把小于的变成黑色（0.0）。

- 反转前景色和背景色。

- 调戏下[`smoothstep()`](https://thebookofshaders.com/glossary/?search=smoothstep)函数，用不同的值来试着做出一个边界顺滑的圆。

- 一旦遇到令你满意的应用，把他写成一个函数，这样将来就可以调用了。

- 给这个圆来些缤纷的颜色吧！

- 再加点动画？一闪一闪亮晶晶？或者是砰砰跳动的心脏？（或许你可以从上一章汲取一些灵感）

- 让它动起来？能不能移动它并且在同一个屏幕上放置多个圆？

- 如果你结合函数来混合不同的距离场，会发生什么呢？

  - ```glsl
    pct = distance(st,vec2(0.4)) + distance(st,vec2(0.6));
    pct = distance(st,vec2(0.4)) * distance(st,vec2(0.6));
    pct = min(distance(st,vec2(0.4)),distance(st,vec2(0.6)));
    pct = max(distance(st,vec2(0.4)),distance(st,vec2(0.6)));
    pct = pow(distance(st,vec2(0.4)),distance(st,vec2(0.6)));
    ```

- 用这种技巧制作三个元素，如果它们是运动的，那就再好不过啦！



#### 添加自己的工具箱

就计算效率而言，[`sqrt()`](https://thebookofshaders.com/glossary/?search=sqrt)函数，以及所有依赖它的运算，都耗时耗力。

[`dot()`](https://thebookofshaders.com/glossary/?search=dot)点乘是另外一种用来高效计算圆形距离场的方式。

```glsl
// Author @patriciogv - 2015
// http://patriciogonzalezvivo.com

#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

float circle(in vec2 _st, in float _radius){
    vec2 dist = _st-vec2(0.5);
	return 1.-smoothstep(_radius-(_radius*0.01),
                         _radius+(_radius*0.01),
                         dot(dist,dist)*4.0);
}

void main(){
	vec2 st = gl_FragCoord.xy/u_resolution.xy;

	vec3 color = vec3(circle(st,0.9));

	gl_FragColor = vec4( color, 1.0 );
}
```



