### 基于塔防性质的roguelike思路

（首先 我不确定塔防加上肉鸽是不是合适 因为聊到这个的起因，是玩了一个steam上塔防肉鸽游戏的demo《最终坚守》）

把塔防变得更加tilebased，就有了地面单元格和墙壁单元格的概念，塔（也就是放在地面上和墙上的那些陷阱）都是可以抽卡定制的

塔防类游戏本身可以利用的设计元素也不多，他也没创造出新的来。虽然有很多是基于游戏内性质的东西，但也不是玩家用得了的。

##### 这个游戏中所作的改动：

1. 使用tilebased，可以采用抽卡的形式改变地面

   这样一来，他放在地上的陷阱和放在墙上的机关攻击，都是在格子上。

   但，「在格子上」只是一个表现，实际上还是应该从「时间轴」的思路出发，可以理解为多久以后在哪个区域有效。

   

   **「时间轴」的思路**：塔防可以看做有一个时间轴，然后对每个小兵，在这个时间轴上移动，然后会遇到攻击，这些攻击是否会碰到“我”（小兵）取决于时间点——如果时间轴走完了没有被攻击打死就扣除玩家的血。(「时间点」举个例子，“我”（小兵）在第30秒-35秒期间会路过一个炮台的射程，如果炮台在30-35秒内有一个开火点（对全部范围内的人造成伤害）就跟我的时间轴有个交点，这个交点就是我掉血的时间点；而我的30-35秒，是我的速度和轨迹决定的。如果速度快的话，是20-22秒路过这里，那么开炮间隔10秒的这个炮台对我来说可能根本不存在)

   ![image-20230224122157542](C:\Users\panyilin\AppData\Roaming\Typora\typora-user-images\image-20230224122157542.png)

   在这个过程中，它的性质只有:

   “我”的血

   “我”的速度

   “我”受击方式（空中，地面之类，背气球的不怕钉板）

   “我”的距离和“轨迹”（也就是逃避一些塔）

   塔防的关卡设计和系统设计都在时间轴上，设计完这个维度再去将其二维化。游戏进行过程中，每波的每个怪物所在的位置是可以用p=f（t）算出来的，设计点主要在于这个函数上。（例如，可以设计怪物的移动方式，对于匀速行走的就是p = kt，比如蛙跳的速度就是个根据t算的抛物线，同理设计不同的移动行为模式。）

2. 它的地板只是传统塔防游戏里的所谓「辐射区」，所以他的地板，无非就是把一个炮台拆成了n个辐射范围，并且每个辐射范围可以diy，tilebased。

也就是说：

1. **它想让人build的，其实是原本塔攻击的辐射范围内的效果**
2. **它用了杀戮尖塔的抽卡模式**

##### 然而，问题在于：塔防的性质很少。

对于塔防来说：

1. 一个怪的速度
2. 一个怪的血量
3. 哪些辐射范围对哪些怪有效

所谓技能，不过是临时的塔而已。

build不出来，是因为：

- 可以利用的性质组合太少

- 效果在塔防并不明显，比如dot之类，最终会被这些性质级别的组合淡化。

  「效果在塔防并不明显」的解释：

  明显的效果，就是直观作用于游戏性质的效果。这里说的「游戏性质」，在塔防里就是：怪的速度、怪的可承受攻击次数（也就是Hit Points，HP的本意）、范围和有效对象。

  比如减速，因为游戏中速度是一个性质，所以速度发生变化很明显，无论减速还是加速，因为是“最直接”的，也是最容易看出来的（这里的「最直接」指的是直接作用于基本性质）；但是比如DOT伤害之类的，因为影响到的是血量变化，它是血量这个性质下产生的东西，所以看起来要“认真”，就不那么明显了。比如“降低目标护甲值”，这种根本就看不出来，哪怕你给他一个视觉特效，因为看不出来的是实际的变化，不是因为肉眼看到有反馈就变化了，而是肉眼能直接看到逻辑的变化才行（但是击飞击退就看得很清楚，只是这并不是塔防应该有的）

  需要注意的是，「肉眼直接看到逻辑的变化」和渲染或者UI没有关系（让你知道今天会下雨，也可以直接看到结果，但是你不会去联想到下雨对于农田的影响，这个影响存在，但不存在于你的“游戏”中）。核心在于，你**能否通过肉眼一眼看出基于性质而发生的变化**，看的是和整个游戏规则的关联度。

  另一个例子：“降低护甲”对于塔防来说有多大的关联？塔防角色没有护甲难道塔防就不能玩了？护甲本身就不是一个性质，但是如果没有了移动速度，那角色就无法移动，角色无法移动就无法构成塔防（没有哪些元素游戏一样转，这些元素就不是基本元素）。

  再来几个例子：
  1，这个游戏里“钉板范围暂时延伸到周围8格”这个明显吗？
  2，“攻击减速的目标的时候，造成的伤害会逐渐提高”？
  3，“命中时，周围所有的塔重置CD”？
  4，“对生命低于20%的目标造成的伤害提高10%”？
  （1, 3是明显的，尽管“玩家不注意看也看不到”）

  为什么「容易看出来更好」：

  比起「明显」，更合适的说法，是让一个属性「**可运算**」。

  举个例子，一个怪有3HP，玩家要击败它，玩家的组合至少得能有机会产生3次hit。通过（范围和频率）这种精确的计算（虽然玩家并不会去精确的计算），就能得出一个最佳方案（通常是凭感觉模糊得出）。

  重要的是“可以判断”。假设每个塔至少能对怪hit一次，我在怪的路线上放了3个塔，怪就必死无疑。也就是说，从怪的hp和路线，就能知道怎么才可能打败怪。

  讨论一下「HP」和「生命低于20%的目标」中，这个「生命」的区别：

  生命值最原本的意义Hit Points，就是我每个小兵被打3下一定死，无论什么打的；你能被hit几下，你可以理解为就是几个点。所以当被换成现代血量的时候，就是偷换了一个核心性质：hit points的本质是可数的，现在变得不可数了，因为虽然你有100血，但是扣血量无从得知（可以从0-9999999999999999）。

  也就是说，性质中的Hit Points的本质是，HP100，就是能被Hit100次。

  这么做的目的，是让玩家可以明确地衡量和意识到自己的策略会对游戏造成怎样的影响。

  但，如果把hp的可运算性变得模糊，就会发生：

  1. 受到伤害的时候短时间内会提高伤害减免，导致掉血是100 98 95 90 77……
  2. 我造成的伤害可能会变动 90-110 而非绝对100 

  理论上还能算出，但是因子太多了，连掰掰手指都不可能。

类似这样的“设计”就会越塞越多，这些设计，都是基于“现代hp”这个性质派生出来的。这些设计在“现代hp”下是有效的，但是“现代hp”并不是塔防应该有的性质——因为塔防中关注的是“打多少下”，这是一个一定要明确的东西，也是一个标尺。
      
现代hp的用途，往往都是一个阈值，你可以堆很多很多很多因子，比如提高攻击力，再比如提高暴击率等等，这些不受技巧或者不完全受技巧影响的元素。  表面上看，却是一个简单的“能不能口算”的问题；但是根基里的区别，是扩展方向。（一个扩展方向是帮玩家明确他思考策略的规则，另一个扩展方向是凭感觉堆数值。根本问题是发展方向不同，被偷换了之后就丢了这个性质，只是新的东西恰好还能支撑而已）

在这个游戏中，卡组是在冷静的环境下build的， 更像是dbg；放陷阱是在一个期望非那么静态的环境来的。

但是现在的问题是游戏运动中，玩家可以做的事情太过少了，一来是资源不够（资源对升级或者造新的，都是捉襟见肘），这就看起来像是放置游戏。二来是，有什么丢什么总不会错（只要不是白痴，丢下去，并且看准轨道等丢，就一定必然最优解，比如减速放在掉血之前都是不会错的，放掉血之后一定是错的）。

突如其来的变化不在玩家计划之中，但是玩家可以应对的手段太有限，这个是由「可以做的事情太少」派生的问题。

基于这些，尝试随便设计一个新的tilebased肉鸽塔防：

1，让玩家丢的地板一定有伤害，因为有伤害肯定是对的。
2，玩家build的是地板的效果，这是由抽卡抽来的东西组合的，也就是说，组合地板的效果，是在战前进行的，是静态的，战斗中，【可能】所有的地板都一样，因此我们只关心这点钱可以丢几个，丢在哪儿。
3，抽出来是陷阱的药引子，然后陷阱允许进行升级，使用药引子，给陷阱加效果，但是加效果的时候，会增加价格。
3.1，我们抽到的东西，是：效果，以及增加的价格。把这些（卡牌）插入陷阱（Slot，阵容，就是塔）得到陷阱，丢场景里。
3.2，阵容，玩家可以有多套，比如3套，那可以组织3个build，然后在关卡内，使用这3种塔来战斗，以便不丢失塔防最基本的感觉。
4，除了资源管理之外，我可以调整塔的3种模式（针对放在场上的塔）：
模式A：正常速度，正常效果
模式B：速度减半，效果为2.5倍
模式C：速度加倍，效果为0.4倍

每个英雄都有3种陷阱（3套build），每个陷阱都有3个插槽，允许插3个强化，升级陷阱，可以+1个插槽。

所以升级的是陷阱，而不是抽到的卡牌。

基础陷阱套件，根据英雄有所差别，举例：
P10的基础陷阱
300块钱 | 5点伤害 | 10点Dot在1秒内 | 0.7秒 | 底板
PP的陷阱
350块钱 | 4点伤害 | 0.3秒 | 地板
小PP的基础陷阱
400块钱 | 6点伤害 | 横竖2格范围、墙壁| 0.4秒

插件举例：

毒药牌
+50块钱 | 造成伤害同时使目标额外加一个1秒内10伤害的DoT
重击牌
+80块钱 | 伤害+10 | CD + 1秒
弹簧牌
+110块钱 | 每第3次伤害导致目标被击飞0.3秒 | CD + 0.4秒
快速零件
+140块钱 | CD 减半 | 直接伤害减半（floor）
节能零件
-300块钱 | 伤害-3（最多留下1） | CD + 0.3秒
扩散零件
+100块钱 | 横竖范围+1 | CD + 1 秒

#### 想想看涉及的数据结构和大概的实现方式：

对于地图上一个地块位置，需要包含的数据：

```c#
public class Terrain : MonoBehaviour
{
    /// <summary>
    /// 所在单元格
    /// </summary>
    public Vector2Int grid;

    /// <summary>
    /// 地形是墙壁还是底板
    /// </summary>
    public TrapPlacement type;
    
}
```

其中TrapPlacement为一个枚举类，即这个地方的地形，也决定了可以装的陷阱的类型，游戏中有两种，也可以视情况继续扩展：

```c#
public enum TrapPlacement
{
    Floor,      //装在墙上
    Wall,       //装在地上
}
```

在地块上，可以放置陷阱块，陷阱块的数据结构应该包含：

位置，在tilebased地图上可以用一个V2Int来记录；

陷阱块的模板struct TrapModel，一个「陷阱的描述」，包括：对上方角色的属性改变（struct CharacterProperty，它是角色属性的描述，包括：血上限、护甲值、移动速度、到终点时造成伤害、打死奖励；）、造成伤害的能力、开火冷却时间、装在哪（enum TrapPlacement）、基础价格（struct Currency[]）、允许升级次数、命中目标之后的函数（List<string>）；

命中后的效果，用一个delegate函数list来存放；

距离下次开火的时间；

当前陷阱的运行方式（低速，中速，高速模式，一个enum）；

当前陷阱升级的级数；

```c#
public class Trap : MonoBehaviour
{
    /// <summary>
    /// 所在单元格
    /// </summary>
    public Vector2Int grid;

    /// <summary>
    /// 模板
    /// </summary>
    public TrapModel model;
    
    /// <summary>
    /// 命中后附带的效果
    /// </summary>
    public List<OnTrapHit> OnHit;

    /// <summary>
    /// 还有多久来一发
    /// </summary>
    public int cooldown;

    /// <summary>
    /// 当前这个陷阱运行的手法
    /// </summary>
    public TrapRunModel runningModel;

    /// <summary>
    /// 当前陷阱升级了多少级
    /// </summary>
    public int level;
    
```

以及两个方法：

负责击中时效果的方法；

和根据运行模式重置陷阱模型冷却时间的方法

```c#
/// <summary>
/// 负责打中效果的
/// </summary>
public void DoHit()
{
    
}

/// <summary>
/// 切换模型冷却时间
/// </summary>
public void SetCooldown()
{
    switch (runningModel)
    {
        case TrapRunModel.Normal: cooldown = model.cooldown;
            break;
        case TrapRunModel.HighRate : cooldown = Mathf.FloorToInt(model.cooldown / 2.0f);
            break;
        case TrapRunModel.LowSpeed: cooldown = Mathf.FloorToInt(model.cooldown * 2.0f);
            break;
    }
}
```

敌人的数据结构，包含它的数据模板和它走的路径struct

```c#
[Tooltip("这个敌人的数据模板")]
public CharacterModel model;

/// <summary>
/// 要走的路
/// </summary>
public PathNodeInfo pathNodeInfo;
```

其中，它的数据模板包括了它的名称、外观prefab路径、角色属性的struct（这个struct也是上面陷阱数据结构中修改的那个）：

```c#
/// <summary>
/// 角色的填表数据
/// </summary>
[Serializable]
public struct CharacterModel
{
    /// <summary>
    /// 角色属性
    /// </summary>
    public CharacterProperty property;

    /// <summary>
    /// 外观
    /// </summary>
    public string prefabPath;

    /// <summary>
    /// 名称
    /// </summary>
    public string name;
}
/// <summary>
/// 角色属性
/// </summary>
[Serializable]
public struct CharacterProperty
{
    /// <summary>
    /// 血上限
    /// </summary>
    public int hp;

    /// <summary>
    /// 护甲值
    /// </summary>
    public int armor;

    /// <summary>
    /// 移动速度
    /// </summary>
    public int speed;

    /// <summary>
    /// 对老家造成的伤害
    /// </summary>
    public int attackPower;

    /// <summary>
    /// 击毙奖励
    /// </summary>
    public Currency[] killReward;
}
```
而路径struct包含要走的节点信息list，和当前节点index


```c#
/// <summary>
/// 地图上敌人会走的路径拐点
/// </summary>
[Serializable]
public struct PathNode
{
    /// <summary>
    /// 路径点
    /// </summary>
    public List<Vector2> nodes;
}

[Serializable]
public struct PathNodeInfo
{
    /// <summary>
    /// 我的路径
    /// </summary>
    public PathNode pathNodes;
    
    /// <summary>
    /// 当前走在第几个节点上
    /// </summary>
    public int currentIndex;
```

##### 游戏运行逻辑：

在GameManager中，包含的变量有：

```c#
/// <summary>
/// 当前的地图
/// </summary>
private Terrain[,] _map;

/// <summary>
/// 所有的敌人
/// </summary>
private List<EnemySoldier> _enemy;

/// <summary>
/// 所有陷阱
/// </summary>
private List<Trap> _traps;

/// <summary>
/// 玩家的老家
/// </summary>
private List<Vector2Int> _home;

/// <summary>
/// 刷怪的波数
/// </summary>
private List<MobWave> _waves;

private int _currentWave;

/// <summary>
/// 游戏运行了多久了
/// </summary>
private int _tickElapsed;

/// <summary>
/// 玩家的HP
/// </summary>
private int _hp;
```

在FixedUpdate中，执行逻辑：

首先，处理所有陷阱：对于陷阱列表中的每个陷阱，判断cd是否结束。如果cd结束，就执行这个陷阱该执行的功能；并且根据它的运行模式，重置它的cd；如果仍在cd中，就继续cd倒计时。

```c#
//处理所有陷阱
foreach (Trap trap in _traps)
{
    if (trap.cooldown <= 0)
    {
        //干该干的

        trap.SetCooldown();
    }
    else
    {
        trap.cooldown -= 1;
    }
}
```

处理所有怪：对于怪列表中的每个怪，判定怪是否已经到达终点。如果已经到达终点，就干掉这个怪，并且触发怪命中老家的效果；否则，执行怪的移动。

```c#
//处理怪
foreach (EnemySoldier enemySoldier in _enemy)
{
    if (Goal(enemySoldier))
    {
        //干掉这个soldier，并且出发命中老家的效果
    }
    else
    {
        //继续走着
    }
}
```

其中，Goal(enemySoldier)是用于判定一个单位是否到达终点的函数：对于传入的某个要判定的敌人，遍历每个老家，将其的grid坐标转换成unity中的transform坐标，判定是否有到达（距离小于一定值）的敌人，返回布尔值。

```c#
/// <summary>
/// 某个角色是否到达终点
/// </summary>
/// <param name="enemySoldier">要判断的角色</param>
/// <returns>是否到达某个终点</returns>
private bool Goal(EnemySoldier enemySoldier)
{
    foreach (Vector2Int home in _home)
    {
        Vector2 hPos = Utils.Utils.GridToPos(home);  
        if (Vector2.Distance(enemySoldier.transform.position, hPos) <= 1.0f)
        {
            return true;
        }
    }
    return false;
}
```

处理刷怪：根据策划设计的时间轴刷怪

```c#
//处理刷怪
if (_currentWave >= 0 && _currentWave < _waves.Count)
{
    int timeElapsedInThisWave = this._tickElapsed - _waves[_currentWave].timeElapsed; //如果玩家按了提前刷怪，这个_wave[_currentWave].timeElapsed是会被临时改写的，改为按键那个时间
    foreach (MobSpawn mobSpawn in _waves[_currentWave].mobSpawn)
    {
        if (mobSpawn.timeElapsed == timeElapsedInThisWave)
        {
            //刷这波，这里是因为tick是单位，所以总有一帧是==的，如果是update模式的话，不能这么用，得写一个这波刷没刷过的判断
        }
    }
}
```

其中，怪物生成的struct：

```c#
public struct MobSpawn
{
    /// <summary>
    /// 这波开始后多久刷这一批
    /// </summary>
    public int timeElapsed;

    /// <summary>
    /// 这波刷的怪
    /// </summary>
    public MobSpawnGroup[] mobList;
}
```

进行胜负判断，如果最后一波最后一个敌人没了，就获得胜利；玩家hp没了，输了；

其他情况下，_tickElapsed++; 

结束本次循环。