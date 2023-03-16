之前的tile还差最后一部分：图像信息。但要想知道图像信息该是什么样子，首先得明确地图上地块的渲染需求是什么样。

##### 地图

最初的设计是做个地图编辑器，类似于填表工具 

设定某张地图的宽度和高度，然后给每个格子填进去地形，就会生成一个用于初始化的填表数据

在运行时，就会通过这个数据生成一个地图Obj

地图上 每个格子是一个struct（上文的tile） 而不是一个指向某条填表数据的索引 这样可以单独对它进行修改

比如某个格子起雾 起火 都只改这一格 而不改其他的草地

但只有数据还不够，地图还需要被人看见，得找个还说得过去的渲染方式。

##### 地图上地块的渲染需求

最简单的情况下，就是单纯的拼接，不需要任何形式的图片处理——

但这样明显很尴尬，平地上长出一个树丛来，地形之间的交接也不够自然。

那么，在已经知道地图上每一格是什么的情况下，要怎么去渲染这个地图？不同的地形交接处，应该有一些自然的过渡，就像rpg maker里地图编辑器做的那样——

[![KSCMV.png](https://i.328888.xyz/2023/03/16/KSCMV.png)](https://imgloc.com/i/KSCMV)



要实现这种方式，需要做一点小改动，采用13宫格的方式来渲染地块。（能对地形间的交接有一些美术上的提升……应该能有吧……）

13宫格是为了在渲染的时候，对于各种形状的边缘交接，都能给出正确的图片，而产生的美术绘制方式。

将一个tile分为四个部分（subtile），对于每个部分，判定与它相邻的块，决定它是处于哪种和其他地块的交接状态，也就是说该显示13宫格的哪个部分图片。

通常，13宫格包含：四个内角、四个外角、四个边，还有一个中间的格子。

[![KS0xN.png](https://i.328888.xyz/2023/03/16/KS0xN.png)](https://imgloc.com/i/KS0xN)



在制作13宫格的时候，最好是一开始根据一个tile的尺寸，算一下要画的图的尺寸，画好一整张然后再切，在使用时候会比较能对上（靠记像素数来分开画每个块，拼的时候对不上不是很尴尬吗）。我把草地作为交接过渡部分的地形美术表现形式，这样对于美术，每个地形只需要画和草地的交接就好了。

有了图之后，

实现这个思路，核心在于某个subtile周围地形种类的判定。

```c#
Scale13[,] subTile = new Scale13[2, 2];
subTile[0, 0] =
    leftTopScale13s[(leftSame == true ? (1 << 2) : 0) | (topSame == true  ? (1 << 1) : 0) | (leftTopSame == true  ? 1 : 0)];
subTile[1, 0] =
    rightTopScale13s[(rightSame == true ? (1 << 2) : 0) | (topSame == true  ? (1 << 1) : 0) | (rightTopSame == true  ? 1 : 0)];
subTile[0, 1] =
    leftBottomScale13s[(leftSame == true  ? (1 << 2) : 0) | (bottomSame == true  ? (1 << 1) : 0) | (leftBottomSame == true  ? 1 : 0)];
subTile[1, 1] =
    rightBottomScale13s[(rightSame == true  ? (1 << 2) : 0) | (bottomSame == true  ? (1 << 1) : 0) | (rightBottomSame == true  ? 1 : 0)];
_map[i, j].SetSubTile(subTile);
```

对于左上subtile，判定的是它左边地块，左上方地块上面地块是否和它是同种地块，根据判定结果决定使用边/内角/外角/内部的美术资源；对于右上subtile，判定的是它上面地块，右上方地块和右边地块是否和他是同种地块；另外两个部分同理。

这里采用了位运算去做数据存储数组的压缩，代替通过index索引list中数据的功能。根据修改特定位置上的值，并把二进制的值和「应该使用哪块地块」对应起来，通过根据各subtile相邻块是否一致判定应该使用哪个地块。将二进制数组和地块对应的过程代码：

```c#
//每个地图块的Scale13[2,2]信息, left<<2 & top<<1 & leftTop, 这和42=101010是一样的原理, 0代表不同，1代表相同
Dictionary<int, Scale13> leftTopScale13s = new Dictionary<int, Scale13>();
leftTopScale13s.Add(0, Scale13.LeftTop);   
leftTopScale13s.Add(1, Scale13.LeftTop); 
leftTopScale13s.Add(2, Scale13.Left);   
leftTopScale13s.Add(3, Scale13.Left);  
leftTopScale13s.Add(4, Scale13.Top);
leftTopScale13s.Add(5, Scale13.Top);
leftTopScale13s.Add(6, Scale13.OutLeftTop);   
leftTopScale13s.Add(7, Scale13.Center);  
```

实现这个功能之后，只需要在逻辑的tilemap生成完成后，调用刚刚写好的渲染方法进行渲染就可以获得交接处渲染比较自然的地图了。

[![KSwnd.png](https://i.328888.xyz/2023/03/16/KSwnd.png)](https://imgloc.com/i/KSwnd)

