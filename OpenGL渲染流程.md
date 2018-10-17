# 渲染流程
  顶点数据->顶点着色器->图元装配->几何着色器->光栅化->裁剪->片段着色器->测试与混合->输出至屏幕
  
# 各阶段的作用
  1. 顶点着色器
     * 把一个单独的顶点作为输入。主要目的是对顶点进行坐标的转换,同时可以对顶点属性进行一些基础的处理。
  2. 图元装配
     * 将顶点着色器输出的所有顶点作为输入。目的是将顶点装配成指定的形状。(有三种形状可以指定点,线,三角形)。
  3. 几何着色器
     * 把图元形式的一系列顶点集合作为输入。他可以通过产生新顶点来构造出新(其他)图元生成其他形状。
  4. 光栅化(像素化)
     * 把矢量图形转化为像素点的过程。即把图元映射为最终屏幕上相应的像素,生成供着色器使用的片段。
  5. 裁剪
     * 裁剪会剔除超出你视图外的像素,提高渲染效率
  6. 片段着色器
     * 主要目的用来计算一个像素最终的颜色。片段着色器中包含光照,阴影,光的颜色等数据,这些数据可以用来计算像素最终的颜色。
  7. 测试与混合
     * 深度测试 用来判断这个像素在其他物体的前面还是后面,从而判断该像素是否丢弃
     * 混合 检查物体的透明值(alpha),让其与物体混合计算出一个最终像素值。
     
# VBO EBO VAO的理解
  1. VBO 顶点缓冲对象。用来管理显存中的顶点数据。
  2. EBO 索引缓冲对象。用来存储绘制顺序
  2. VAO 顶点数组对象。其中可以包含多个配置顶点属性指针(VAO中包含多个VBO)。这样切换绘制物体即切换对应的VAO即可。
     每次切换绘制物体就要对应切换配置的顶点属性指针。如果一个顶点包含多个顶点属性指针的配置,切换一次绘制就要切换多个顶点属性配置,这样很麻烦，将多个配置顶点属性指针
     放进VAO中，这样切换绘制的时候切换对应的VAO即可。
     
# 使用VBO绘制一个物体的流程
```cpp
// 0. 复制顶点数组到缓冲中供OpenGL使用
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
// 1. 设置顶点属性指针
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
// 2. 当我们渲染一个物体时要使用着色器程序
glUseProgram(shaderProgram);
// 3. 绘制物体
someOpenGLFunctionThatDrawsOurTriangle();
```

# 使用VBO EBO VAO 绘制一个物体的流程
```cpp
// ..:: 初始化代码 :: ..
// 1. 绑定顶点数组对象
glBindVertexArray(VAO);
// 2. 把我们的顶点数组复制到一个顶点缓冲中，供OpenGL使用
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
// 3. 复制我们的索引数组到一个索引缓冲中，供OpenGL使用
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
// 4. 设定顶点属性指针
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

[...]

// ..:: 绘制代码（渲染循环中） :: ..
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0)
glBindVertexArray(0);
```

# OpengGL数据如何关联到着色器(链接顶点属性)
```cpp
//第一个参数指定我们要配置的顶点属性。如果我们在顶点着色器中使用layout(location = 0)定义了position顶点属性的位置值(Location)。它可以把顶点属性的位置值设置为0。因为我们希望把数据传递到这一个顶点属性中，所以这里我们传入0。
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
//顶点属性位置值作为参数，启用顶点属性；顶点属性默认是禁用的
glEnableVertexAttribArray(0);
```


# 使用OpenGL绘制一个三角形的流程

  
