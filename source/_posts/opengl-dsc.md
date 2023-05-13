title: opengl dsa
author: xuezc
tags:
  - opengl
categories:
  - opengl
  - graph
abbrlink: e83f7fae
description: 'opengl Direct State Access与之前的示例,使用到的api方便搜索'
date: 2023-05-10 08:32:00
---
# opengl Direct State Access
传统的OpenGL API是基于“绑定”（Binding）概念的，即需要通过调用类似glBindBuffer、glBindTexture、glBindFramebuffer等函数将状态绑定到OpenGL上下文中，并且在操作状态前需要确保该状态已经被正确地绑定。而DSA则是通过直接访问OpenGL对象的状态，而不是将状态绑定到上下文中，从而减少了状态切换的开销和降低了代码的复杂度。
## without DSA
### vao&vbo
生成并绑定vao
```
unsigned int VBO, VAO;
glGenVertexArrays(1, &VAO);
glBindVertexArray(VAO);
```
生成并绑定vbo
```
glGenBuffers(1, &VBO);	
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```
设置顶点属性指针,vbo的glBufferData不一定需要绑定vao,但是下面的数据必须绑定vao
```
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```
### texture
```
glGenTextures(1, &textureID);
glBindTexture(GL_TEXTURE_2D, textureID);
glTexImage2D(GL_TEXTURE_2D, 0, format, width, height, 0, format, GL_UNSIGNED_BYTE, data);
glGenerateMipmap(GL_TEXTURE_2D);

glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, format == GL_RGBA ? GL_CLAMP_TO_EDGE : GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, format == GL_RGBA ? GL_CLAMP_TO_EDGE : GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```
### framebuffer
```

```

## with DSA
### vao&vbo
生成并绑定vao
```
glCreateVertexArrays(1, &vao);
glBindVertexArray(vao);
```
生成并绑定vbo,vbo的数据的绑定合并了之前的glBindBuffer和glBufferData两个函数
```
glCreateBuffers(1, &vbo_);
glNamedBufferData(vbo_, static_cast<GLsizeiptr>(size_in_byte_), init_data, GL_STATIC_DRAW);
```
将顶点属性数组与顶点缓冲对象进行绑定
```
glVertexArrayVertexBuffer(VAO, 0, VBO, 0, 3 * sizeof(float));
```
设置顶点属性指针
```
glVertexArrayAttribFormat(VAO, 0, 3, GL_FLOAT, GL_FALSE, 0);
glVertexArrayAttribBinding(VAO, 0, 0);
glEnableVertexArrayAttrib(VAO, 0);
```
### texture
```
glCreateTextures(GL_TEXTURE_2D, 1, &textureID);
glBindTextureUnit(0, textureID);
glTextureStorage2D(textureID, 1, GL_RGBA8, width, height); // 分配纹理内存
glTextureSubImage2D(textureID, 0, 0, 0, width, height, format, GL_UNSIGNED_BYTE, data); // 上传纹理图像数据
glGenerateTextureMipmap(textureID); // 自动生成纹理的多级渐远纹理

glTextureParameteri(textureID, GL_TEXTURE_WRAP_S, format == GL_RGBA ? GL_CLAMP_TO_EDGE : GL_REPEAT); 
glTextureParameteri(textureID, GL_TEXTURE_WRAP_T, format == GL_RGBA ? GL_CLAMP_TO_EDGE : GL_REPEAT);
glTextureParameteri(textureID, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTextureParameteri(textureID, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```
# 内置变量
记录一些内部的变量,比方gl_VertexID在glsl中的序号即为glVertexAttribPointer的序号.所以glVertexAttribPointer函数之前要绑定vao确定数据位置.
```
attribute vec4 gl_Color;              // 顶点颜色
attribute vec4 gl_SecondaryColor;     // 辅助顶点颜色
attribute vec3 gl_Normal;             // 顶点法线
attribute vec4 gl_Vertex;             // 顶点物体空间坐标（未变换）
attribute vec4 gl_MultiTexCoord[0-N]; // 顶点纹理坐标（N = gl_MaxTextureCoords）
attribute float gl_FogCoord;          // 顶点雾坐标
```