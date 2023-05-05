# 练习



#### 矩形

1. 第一步 知道怎么描边,这样可以获取一个纯色矩形

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    vec2 p1 = step(0.1,st);
    vec2 p2 = step(0.1,1.0-st);
    color = vec3(p1.x * p1.y * p2.x * p2.y);
    
    gl_FragColor = vec4( color, 1.0 );
}
```



#### 矩形边框

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    vec2 p1 = step(0.1,st);
	vec2 p2 = step(0.1,1.0-st);
    float p3 = p1.x * p1.y * p2.x * p2.y;
    
    vec2 x1 = step(0.15,st);
	vec2 x2 = step(0.15,1.0-st);
    float x3 = x1.x * x1.y * x2.x * x2.y;
    
    color = vec3(p3 - x3);
    
    gl_FragColor = vec4( color, 1.0 );
}
```



#### 多个矩形

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    vec2 p1 = step(0.1,st);
	vec2 p2 = step(0.1,1.0-st);
    float p3 = p1.x * p1.y * p2.x * p2.y;
    
    vec2 x1 = step(0.15,st);
	vec2 x2 = step(0.55,1.0-st);
    float x3 = x1.x * x1.y * x2.x * x2.y;
    
    vec2 y1 = step(0.55,st);
	vec2 y2 = step(0.15,1.0-st);
    float y3 = y1.x * y1.y * y2.x * y2.y;
    
    vec2 z1 = step(vec2(0.15,0.55),st);
	vec2 z2 = step(vec2(0.55,0.15),1.0-st);
    float z3 = z1.x * z1.y * z2.x * z2.y;
    
    vec2 w1 = step(vec2(0.15,0.55),1.0-st);
	vec2 w2 = step(vec2(0.55,0.15),st);
    float w3 = w1.x * w1.y * w2.x * w2.y;
    
    color = vec3(p3 - x3 - y3 - z3 - w3);
    
    gl_FragColor = vec4( color, 1.0 );
}
```





#### 圆形

```glsl
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

void main(){
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec3 color = vec3(0.0);

    float bw1 = step(0.45,distance(vec2(0.5),st));
    color = vec3(bw1);
    
    gl_FragColor = vec4( color, 1.0 );
}
```





