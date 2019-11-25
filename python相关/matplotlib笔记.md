# **Matplotlib**

中文乱码

```
from matplotlib import pyplot as plt
plt.rcParams['font.sans-serif']=['SimHei']
```

升级pip

```python -m pip install --upgrade pip```

### numpy

#####1.常用方法

linspace用法

```python
>>> import numpy as np
>>> np.linspace(2,20,10)  # 输出[2,20]中等距的值
array([ 2.,  4.,  6.,  8., 10., 12., 14., 16., 18., 20.])
```

### 散点图

##### 1.scatter函数原型

**matplotlib. pyplot.** **scatter**(x, y, s=20, C= 'b', marker= 'o', cmap=None, norm=None, vmin=None, vmax=None, alpha=None,linewidths=None, verts=None, hold=None, **kwargs)

绘制散点图，其中X和Y是相同长度的数组序列

|             x,y:                |              形如shape(n,)数组                        |        输入数据                                                        |
| :-----------------------: | :-----------------------------------: | :----------------------------------------------------------: |
|            **s**:         | 标量或形如**shape(n,)**数组，可选,默认:**20** |                    **size in points^2**                    |
|            **C**:         |      色彩或颜色序列，可选，默认       | 注意**C**不应是一个单一的**RGB**数字或**RGBA**序列，因为不便区分。**C**可以是一个**RGB**或**RGBA**二维行数组 |
|          **marker**:          |      MarkerStyle,可选，默认:‘**o**'   |                     详情参阅**markers**属性                  |
|           **cmap**:           |        Colormap可选，默认:**None**        |                         **Colormap**实例                         |
|           **norm**:           |       Normalize可选，默认:**None**    |                    数据亮度**0-1,floa**t数据                 |
|        **vmin,vmax**:     |         标量，可选，默认:**None**         |            亮度设置，若**norm**实例已使用，该参数无效            |
|          **alpha**:       |         标量，可选，默认:**None**         |                             **0-1**                          |
|        **linewidths**:        |         标量或数组，默认:**None**         | **......** |

**matplotlib.pyplot.sci(im)**

#####2、其中散点的形状参数marker如下：

|   marker   |  description   |    描述    |
| :--------: | :------------: | :--------: |
|    "."     |     point      |     点     |
|    ","     |     pixel      |    像素    |
|    "o"     |     circle     |     圈     |
|    "v"     | triangle_down  |  倒三角形  |
|    "^"     |  triangle_up   |  正三角形  |
|    "<"     | triangle_left  |  左三角形  |
|    ">"     | triangle_right |  又三角形  |
|    “1”     |    tri_down    |  tri_down  |
|    “2”     |     tri_up     |   tri_up   |
|    "3"     |    tri_left    |  tri_left  |
|    “4"     |   tri_right    | tri_right  |
|    “8"     |    octagon     |   八角形   |
|    "s"     |     square     |   正方形   |
|    "P"     |    pentagon    |    五角    |
|    "*"     |      star      |    星星    |
|    "h"     |    hexagon1    |   六角1    |
|    "H"     |    hexagon2    |   六角2    |
|    "+"     |      plus      |    加号    |
|    "x"     |       x        |    x号     |
|    "D"     |    diamond     |    钻石    |
|    "d"     |  thin_diamond  |    细钻    |
|    "\|"    |     vline      |    v线     |
|    "_"     |     hline      |    H线     |
|  TICKLEFT  |    tickleft    |   左刻度   |
| TICKRIGHT  |   tickright    |   右刻度   |
|   TICKUP   |     tickup     |   上刻度   |
|  TICKDOWN  |    tickdown    |   下刻度   |
| CARETLEFT  |   caretleft    | caretleft  |
| CARETRIGHT |   caretright   | caretright |
|  CARETUP   |    caretup     |  caretup   |
| CARETDOWN  |   caretdown    | caretdown  |
|   “None"   |    nothing     |     无     |
| None                   | nothing                               | 无                          |
| " " | nothing                               | 无                          |
| ”“                  | nothing                               | 无                          |
| '$ $' | render the string using mathtext     | 使用mathtext渲染的字符串。  |
| verts                  | a list of(x,y) pairs used for Path vertices. | 用于路径顶点(X,Y)对的列表。 |
| path                   | a Path instance.                      | 一个路径实例。              |
| (numsides,style,angle) | see below                             |                             |

#####3、其中颜色参数c如下:

b---blue		c---cyan		g---green	 k---black

m---magenta		r---red		w---white	  y---yellow

##### 4、一些参数

ha 水平  va 垂直