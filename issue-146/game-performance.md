# 游戏性能：规划限定条件

今天，我们要分享一些使用OpenGL着色语言（GLSL）的最佳实践，可以优化你的游戏性能并简化你的工作流程。具体来说，规划限定条件会使你的代码更具确定性并通过减少你的工作量来提高性能。

让我们开始于一个简单的顶点着色器，并随着不断进行而改变它。

这个基本的顶点着色器需要位置和纹理坐标、转换位置和输出数据到片段着色器：

```
attribute vec4 vertexPosition;
attribute vec2 vertexUV;

uniform mat4 matWorldViewProjection;

varying vec2 outTexCoord;

void main()
{
  outTexCoord = vertexUV;
  gl_Position = matWorldViewProjection * vertexPosition;
}
```

## 顶点属性指针

为了在屏幕上画一个网格，你需要创建一个顶点缓冲并用顶点数据填充它，对于本实例，包括位置和纹理坐标数据。

在着色实例中，顶点数据可以布置这样：

```
struct Vertex
{
  Vector4 Position;
  Vector2 TexCoords;
};
```


因此，我们根据如下所述定义顶点着色器属性：

```
attribute vec4 vertexPosition;
attribute vec2  vertexUV;
```

为了将顶点数据和着色器属性结合起来，对glGetAttribLocation 的调用将获得命名属性的句柄。随着对glVertexAttribPointer的调用，然后列出了详细的属性格式。

```
GLint handleVertexPos = glGetAttribLocation( myShaderProgram, "vertexPosition" );
glVertexAttribPointer( handleVertexPos, 4, GL_FLOAT, GL_FALSE, 0, 0 );

GLint handleVertexUV = glGetAttribLocation( myShaderProgram, "vertexUV" );
glVertexAttribPointer( handleVertexUV, 2, GL_FLOAT, GL_FALSE, 0, 0 );
```

你可以获得多个带有vertexPosition 属性的着色器，并为每个着色器调用glGetAttribLocation 会降低性能，但这会增加你的游戏加载时间。

使用规划限定条件，你可以像如下那样改变你的顶点着色器属性声明：

```
layout(location = 0) in vec4 vertexPosition;
layout(location = 1) in vec2 vertexUV;
```

为了达成这个目的，你还需要告诉着色器编译器你的着色器是针对GL ES3.1版本。这是通过添加一个版本声明实现的：

```
#version 300 es
```

让我们看看如何影响着色器，变化部分以粗体标记：

```
#version 300 es

layout(location = 0) in vec4 vertexPosition;
layout(location = 1) in vec2 vertexUV;

uniform mat4 matWorldViewProjection;

out vec2 outTexCoord;

void main()
{
  outTexCoord = vertexUV;
  gl_Position = matWorldViewProjection * vertexPosition;
}
```

应当注意到，我们还从变更到输出改变了outTexCoord。从版本300es开始就已经无法变更关键词，并需要更换着色器进行工作。

应当注意到，**Vertex Attribute qualifiers**和版本300es都支持OpenGL ES 3.0。等效的桌面支持OpenGL 3.3并使用330版本。

现在你知道你的位置属性总是在0和你的纹理坐标将在1，现在你可以不使用[glGetAttribLocation](https://www.khronos.org/opengles/sdk/docs/man3/html/glGetAttribLocation.xhtml)而约束你的着色器格式。

```
const int ATTRIB_POS = 0;
const int ATTRIB_UV   = 1;

glVertexAttribPointer( ATTRIB_POS, 4, GL_FLOAT, GL_FALSE, 0, 0 );
glVertexAttribPointer( ATTRIB_UV, 2, GL_FLOAT, GL_FALSE, 0, 0 );
```

这个简单的变化会导致一个更清洁的通道、简化的代码，并在加载期间保持了性能。

Android性能扩展[Android Performance Patterns series](https://www.youtube.com/watch?v=HXQhu6qfTVU&list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE)