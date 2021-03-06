---
layout: default
title: 浅谈计算几何学中向量的一些应用
---


本文将讨论二维平面中向量的一些简单应用。


### 向量的定义和表示  

向量(Vector, 也称作矢量)，由方向和长度两个因素组成。在二维平面中，向量和点的表示方法是一样的。例如，一个二维向量可以简单地表示为：

$$  
\vec{a}=(x, y) 
$$  

向量的表示方法虽然和点一样，但是它们的意义是不同的。向量之间有一些运算，比如：向量+向量=向量，点+向量=点，向量-向量=点，点-点=向量，而点+点这个运算没有任何意义。


### 向量的线性运算  

向量的线性运算包括向量的加法、减法和数乘三种运算，下面直接给出 C++ 代码：

```c++
struct point
{
  double x, y;
  point(): x(0), y(0) {}
  point(double x, double y): x(x), y(y) {}
  point operator + (point rhs) //向量加法
  {
    return point(x + rhs.x, y + rhs.y);
  }
  point operator - (point rhs) //向量减法
  {
    return point(x - rhs.x, y - rhs.y);
  }
  friend point operator * (point lhs, double rhs) //向量数乘
  {
    return point(lhs.x * rhs, lhs.y * rhs);
  }
  friend point operator * (double lhs, point rhs) //向量数乘
  {
    return point(lhs * rhs.x, lhs * rhs.y);
  }
};
```


### 向量的点积  

点积，又被称为内积、数量积。顾名思义两向量的点积结果是一个数量。设向量 $$ \vec{a}=(x_1, y_1) $$ 和向量 $$ \vec{b}=(x_2, y_2) $$ 在同一平面内，其数量积为: 

$$  
\vec{a}\vec{b}= x_1x_2 + y_1y_2 
$$  

其 C++ 代码实现为:
```c++
  double dot(point rhs) //向量点积
  {
    return x * rhs.x + y * rhs.y;
  }
```

那么，点积有什么具体的应用呢？让我们考虑下如何判断两条直线是否垂直（给出过直线的两点）。一个比较简单的想法是求出两条直线的方程，然后判断系数之间的关系。这个方法显然有些麻烦，计算量比较大。

不妨假设这两条直线分别为 AB 和 CD 。这时候我们再考察一下向量 $$ \vec{AB} $$ 和向量 $$ \vec{CD} $$ ，这两向量正是两线段的方向向量，如果这两向量的数量积为零，那么这两条直线垂直。这种方法的具体实现和计算量都非常小，只要计算向量的点积，而且精度误差也较小。


### 向量的叉积  

叉积，又被称为外积、向量积。显然和点积的结果不同，叉积的结果是一个向量，其方向需要用右手定则来确定。

首先给出二维计算几何中向量叉积的计算方法：

设向量 $$ \vec{a}=(x_1, y_1) $$ 和向量 $$ \vec{b}=(x_2, y_2) $$ 在同一平面内，则:

$$  
\vec{a}\times \vec{b}=x_1y_2 – x_2y_1 = |\vec{a}||\vec{b}| \sin x
$$  

其C++代码实现为:
```c++
  double cross(point rhs) //向量叉积
  {
    return x * rhs.y - y * rhs.x;
  }
```

那么向量叉积具体有什么应用呢？让我们首先思考这样一个问题，已知平面上一三角形的三点(ABC)坐标，如何计算这个三角形的面积。

首先一个简单的思路是取其中两点连起来作为底边，然后求另一点到底边的距离（高），然后根据 $$ 面积=\frac{底\times高}{2} $$  来计算三角形的面积。这个方法似乎很简单，但求高似乎是一个比较麻烦的问题。

其次一个简单粗暴的思路是利用海伦公式，直接求解三角形面积。这个方法比上一个思路简单很多了，但是其中有一个开平方的操作，这样带来的精度误差就比较大了。

还有一个求三角形面积的公式是 $$ S = \frac{\mid a \mid \mid b \mid \sin C}{2} $$ 。这个方法比海伦公式更加简洁了，唯一的麻烦在于 $$ \sin C $$。这时候我们不妨回过头来看一下向量叉积的定义式，显然，求三角形的面积便可以用以下公式进行求解：

$$  
S = \frac{|\vec{a} \times \vec{b}|}{2}
$$  

利用上述公式，我们只需要减法、乘法、除法操作便能求出三角形的面积，不仅简单粗暴，而且精度损失也是非常小的。

事实上，叉积求的便是对应平行四边形的有向面积。

有了三角形面积的求解方法，让我们想一想多边形的面积该如何求解。这里的多边形按逆时针顺序给出其各个顶点的座标。

显然我们无法直接求解任意多边形的面积，不妨用化归转移的思想，我们把这个多边形剖成若干个三角形，我们只要求出各个三角形的面积，把它们加起来即可。由于用向量求出的是有向面积，我们只要按逆时针顺序直接做向量的叉积，而且也可以处理凹多边形。

上文笔者一直在强调有向，那么有向这个概念是如何体现的呢？我们不妨考虑一下这个问题：已知平面上一线段 AB，问点 P 和 AB 的位置关系。P 在有向线段 $$ \vec{AB} $$ 的左边？右边？还是所在直线上？

事实上我们只要求一次有向面积即可：计算 $$ \vec{AP} \times \vec{AB} $$ ，如果结果为正，则在左边；结果为负，则在右边；如果结果为零，那么 P 位于 AB 所在直线上。

为什么呢，因为这里的方向其实是角度的方向：规定逆时针角度为正，顺时针角度为负。

这种判定，在计算几何学中通常被称为“to-left-test”，顾名思义就是判断点是否位于有向线段的左侧。

让我们不妨把问题扩展一下，如何判断点与三角形的关系？点位于三角形内部还是外部（为了简单起见，我们不考虑点位于三条边所在直线上）。

假设三角形三顶点为 ABC，我们只要对点 P 与有向线段 $$ \vec{AB} $$ ,  $$ \vec{BC} $$, $$ \vec{CA} $$ 做三次 to-left-test ，如果三次 to-left-test 的结果相同，那么就说明点 P 位于 $$ \triangle ABC $$ 的内部。

巧合的是，三次 to-left-test 一共有8种结果，而平面上三条直线能将一个平面分隔成7个部分，那么消失的那种情况在哪里呢?如果在曲面上，结果又是如何呢？

---
参考资料：  
[1]. 算法导论（原书第3版）/（美）科尔曼（Cormen, T.H.）等著；殷建平等译；—北京：机械工业出版社，2013.  
[2]. 算法竞赛入门经典——训练指南/刘汝佳，陈锋编著. —北京： 清华大学出版社，2012.  
[3]. 计算几何/邓俊辉主讲. —学堂在线 MOOC 平台，2015.
