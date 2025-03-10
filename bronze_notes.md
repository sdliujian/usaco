# USACO刷题笔记
## [USACO24DEC] Roundabount Rounding B
### 题意概括：四舍五入 vs 链式舍入
### 题解：打表找规律
1. 暴力枚举

  $T(T<=10^5)$ 个用例，每个用例输入数字 $N <= 10^9$。
  暴力枚举复杂度是 $O(T*N)$ 必超时

2. 通过打表找规律
```
45 46 47 48 49
445 446 447 448 449 450 451…… 497 498 499
4445 4446 4447 4448 4449 4450 4451 …… 4997 4998 4999
……
```
【规律】如果前面全是4，后面紧跟一个5-9，之后是任何数都行，那么两种进位的结果不同
这是个充要条件。

总之 [44444…5, 49999…9] 都可以.

不难得出如下的规律。由于数据范围不大，对每个区间的左端和右端可以打表来进行处理。
```
int fir[10]=
{0,45,445,4445,44445,444445,4444445,44444445,444444445,4444444445};
int sec[10]=
{0,50,500,5000,50000,500000,5000000,50000000,500000000,5000000000};
//这里用的是左闭右开区间
```

那么接下来只需对 N 进行搜索即可，看其是否在当前区间内。如果小于区间左端就代表搜索完毕，输出答案。如果大于区间右端就加上这个区间的数的个数，并搜索下一个区间。如果在当前区间内，就加上当前区间内小于等于 N 的数的个数并输出。

## [USACO24DEC] Farmer John's Cheese Block B
### 题意概括：立方体形状的奶酪，挖去一些小块后，会不会出现贯穿的空洞

### 题解：利用map优化查询
map不一定要用STL的map/unordered_map, 数组也可以实现map

如果map <key, value> 的key是范围比较小的整数，那么就可以用数组表示map
否则如果key是字符串、比较大的整数或其他复杂类型，那么就用STL

  1. 暴力

直接的方法是用三维数组表示立方体，然后记录每个块是否还在。

很显然当 N≤100 时，可以开数组标记是否已经被挖掉。判断那些位置可以插入木棒时，
$O(N^2)$ 枚举(x,y)，(x,z) 和 (y,z)，然后 $O(N)$ 判断是否可以插入。复杂度 
$O(N^3 * Q)$。

  2. 对暴力进行优化
  
  因为是在不停地抠奶酪，所以不难发现答案是单调不减的。

  发现对于每一组相同的 (x,y) 或 (x,z) 或 (y,z) 坐标，当且仅当这一条被覆盖了 N 次后，才会对答案产生 
+1 的贡献。开三个 map(二维数组表示即可) 记录一下，每次更新的时候判断一下是否满足条件即可。

## [USACO24DEC] It's Mooin' Time B
### 题意概括：moo出现了F次，就可能是Bessie发出的，但是有一个字母可能出错
### 题解：暴力枚举，枚举所有可能出错的位置和出错后可能变成的字母
暴力枚举经常有多种枚举方案，选择简单的。
  
  一种哞叫一般地定义为子串$(c_i c_j c_j)$， 其中某字符 $c_i$ 之后紧跟着 2 个某字符 $c_j$ ，且 $c_i≠c_j$ 。根据 Farmer John 的说法，Bessie 哞叫了很多，所以如果某种哞叫在竞赛中出现了至少 F（1≤F≤N）次，那可能就是 Bessie 发出的

  这个题目可能有多种思路：
  1. 一种是对于每一个位置，判断能否和后两个位置组成一种声音moo，或将三个字母中的一个修改后组成一种声音，然后统计这个声音出现的次数。 
  
     moo, mox, mxo, xoo 多种可能都要考虑，这种写法需要考虑的细节比较多。而且在不同的位置修改字母之后进行的统计会出现大量的重复。复杂度大约是 $O(N^2)$
     
  这种思路比较直接，但是一想就觉得比较乱。
  如果在统计某一种moo出现次数多的时候，想到使用map，就能够避免重复统计的情况。
  
  2. 第二种是对于每一种声音（将其命名为 moo ），统计出现次数
    时间复杂度$O(26 * 26 * N)$
 
   注意，例如枚举到 abb，假如在字符串中遇到了 abb，那么出现次数 
+1，假如遇到了 abx，那么要分讨一下：

  1）出现了 ababb (abx就是开头的aba)，那么用掉修改次数显然不优，不如不用。
  2）否则直接修改成abb，abb出现次数增加 1。
  
  同理，若遇到了 axb：
  1）出现的是 aabb (axb就是开头的aab)，那么不用修改次数
  2）否则直接修改成abb
  
  若遇到 xbb，直接修改成abb
  
  这种思路也比较复杂，需要想清楚上面几种分情况讨论的细节。
  
  3. 遍历一遍字符串，统计每一种moo出现的次数
  
  只要用一个map，就能记录每一种moo出现的次数了，复杂度$O(N)$
  
  那么错一个字母的情况怎么处理呢？
  最简单的方法：枚举每一个位置出错的情况，出错前可能变成另外25个字母中的任何一个。
      
  - 如果出错前不能形成新的moo，那么对结果没有影响；
  - 如果出错前形成了新的moo，则新的moo次数+1; 
  - 如果该位置本来就构成moo，那么旧的moo次数-1。
  
  其实对每一个位置，不需要枚举所有的字母，只要枚举能够改变结果的情况就可以了。
  这个算法的复杂度也是O(N)
    
  > Tips: 如果设计的算法太复杂，需要考虑很多细节，那么先想想是否有更好的方法。
  
  > Tips: 开始写程序之前，最好能够用注释文字写一下算法步骤概要，这样程序写起来比较快，不容易出错。
  例如：// 1. ....
  // 2. ....
  // 3. ....
    如果算法太简单，也可以直接开始写。
[USACO24OPEN] Logical Moos B

暴力方法有大量重复计算，可以通过预处理把很多结果存起来，每次计算的时候只要查询结果就可以。

5 7
false and true or true
1 1 false
1 3 true
1 5 false
3 3 true
3 3 false
5 5 false
5 5 true

NYYYNYY

把整个表达式按 or 分隔成若干块，可以 
O(n) 预处理出：

每一块的值；
每一个 true 或 false 在哪一块；
每一个 true 或 false 所在的块前面 and 起来的值；
每一个 true 或 false 所在的块后面 and 起来的值；
每一个 true 或 false 所在的块前面所有块的值 or 起来的值；
每一个 true 或 false 所在的块后面所有块的值 or 起来的值。
将上面提到的六个值用六个数组存起来，下面分别把它们命名为 
ai ,bi,bpi ,bsi ,api ,asi 。

预处理后，对于每个询问，输入完l,r,x，接着就可以这样 O(1) 计算最后的值
 
 [USACO24OPEN] Walking Along a Fence B
 前缀和 , 把任意区间和计算的复杂度从O(N) 减小的 O(1)
 a:          1 2 3 4  5  6  7
 prefix_sum: 1 3 6 10 15 21 28
 
 任意区间和 可以推广到 任意区间的距离
 
 发现x,y 小于等于 1000，可以直接把每个点前的第一个柱子位置和编号存下来。
再用一个前缀和数组记录下每两个柱子之间的距离。

要求出两个点之间的距离，可以先找到对应的栅栏的起始点，然后计算它们之间的距离 
d，即 dis[y]-dis[x]（如果y 在 x 前面则需要交换这两个点），再分别计算这两个点到栅栏起始点的距离 
dis1 ,dis2，距离就是d−dis1 +dis2。

最后只需要输出 max(d−dis1 +dis2 ,disn −(d−dis1 +dis2)) 即可。

[USACO24OPEN] Farmer John's Favorite Permutation B

遇到困难的时候，一定不要只是闷头思索，而要在纸上写几个用例，或者画图描述题意，帮助自己思考。

3 1 2 4


1） 每次去掉两个中更大的一个数，最后必然剩下 1，所以 h n−1  必须为 1，否则无解。

2） 1 最多只会被输出两次，即左边删掉的一次和右边删掉的一次。如果有超过两次则也无解。

其它数最多只会被输出一次，因为另一边有一个 1 挡着呢。如果一个非 
1 的数被输出两次，则也无解。

无解的情况分析完了，那么怎么构造呢？

注意到刚开始在最两边的数（如果不是1 ）不会被输出。可以把不出现的的两个数（或者一个数，如果 1只出现了一次）挑出来，先放到数列的两端。要求字典序最小的方案，则将更小的那个安排在前面。
如果只有一个数没被输出，说明 1 在数列的最前面。
接下来按 h 数组顺序安排元素就行。每次判断当前的左右两个数谁更大，再把 
h 的新元素放到那边。

[USACO23DEC] Candy Cane Feast B

这是一道标准的模拟。

我们记录每个糖果棒的下端位置 si 和上端位置 ei 。初始时所有的 
si  都为 0。

当每一头奶牛走向糖果棒时，如果 ai <si，则该奶牛吃不到糖果棒。跳过。

否则，分情况讨论：
如果它没有吃完糖果棒，也就是 
a i <e i ，则它会长 ai −si  个单位高度，同时把 si
  更新为 ai 。

否则，它吃完了糖果棒，即 
a i ≥s i，则它会长 e i  −s i 个单位高度，同时把 si  更新为 
e i 。

时间复杂度为 Θ(n×m)，无法通过 1≤n,m≤2×10 5的数据。

一个简单但又很有效果的优化是：当一根糖果棒被吃完时，我们就可以 break，没必要再做下去了。

这样为什么是对的呢？应为对于一根糖果棒，它有以下两种状况：

全被 1 号牛吃了。

没有全被 1 号牛吃完。

每出现一次二号情况，
1 号牛的身高就会翻一倍。而糖果最高也只有 
10 9 ，所以最多出现 
log10 9=30 次二号情况后就全是一号情况了。而一号情况的复杂度是 
Θ(1) 的，所以这个复杂度是对的

[USACO23DEC] Cowntact Tracing 2 B
为了让最开始感染的牛尽量少，我们要让传染的天数尽量多。我们可以先求出这个天数，再根据天数来倒推最开始最少的感染牛数。

通过思考可以发现，连续 x 只感染的牛最多通过 ⌊ (x−1)/2 ⌋ 天的传染得到（想象一只牛在中间，每天都把两边的牛传染到）。
通过样例二可以发现，整群牛最多传染天数是所有连续的牛的最多传染天数中的最少天数。

让我们假设这个求出来的天数是 m。确定天数后，我们就可以倒推牛数了。每只牛在 
m 天后会传染左面、右面各 m 只牛，算上自己，会传染 2m+1 只牛。
因此，为了最初感染的牛最少，我们每 2m+1 只牛就只把中间的那头牛当最初感染的牛。这样的话，连续 
x 只感染的牛中，最少会有 ⌈ x/(2m+1) ⌉ 只是最初感染的。

最后是要注意边界，因为边界的情况与中间不太一样。在边界，连续 
x 只牛能传染 x−1 天。

[USACO23DEC] Farmer John Actually Farms B
数学思维

因为不同整数构成，所以数组 t 是一个排列，换句话说这些竹笋按照 
t 从大到小排列后高度为严格升序。

一个序列是严格升序当且仅当每两个相邻的元素，前面的元素都小于后面的元素。

对于这个序列，求出每两个相邻元素满足要求的时间区间，再对这若干个区间求交即可。

满足要求的时间区间解一元一次不等式即可，分类讨论一下。

区间求交就是右端点取最大值，左端点取最小值，若最后区间能为合法区间，则左端点即为最早合法天数，否则一定不合法。

 [USACO24JAN] Majority Opinion B
题目看似复杂，其实只需要对每 3只相邻的奶牛进行考虑。显然，只要任意三只相邻奶牛中，有两只奶牛干草喜好相同，这种喜好就能通过向外“扩散”统一到所有奶牛。
遍历所有三只相邻奶牛，并用 vector 数组存储满足上述条件的干草，最后大胆 sort 排序即可。
另外，对于 n=2 的情况，只须特判两只奶牛干草喜好是否相同即可。
 [USACO24JAN] Cannonball B
考虑暴力模拟，判环的话就是记 Si  为第 i 个位置下一步跳到的位置的集合，如果下一步要跳到的位置为 x，若 x∈Si ，则之前进行过这样的操作，就重复了，退出即可。
时间复杂度为 O(NlogN)。

[USACO24JAN] Balancing Bacteria B
差分


>微信公众号排版工具。问题或建议，请公众号留言。**[程序员翻身](#jump_8)**

建议使用 **Chrome** 浏览器，体验最佳效果。

使用微信公众号编辑
- 支持自定义样式的 Markdown 编辑器
- 支持微信公众号、知乎和稀土掘金
- 点击右上方对应图标，一键复制到各平台

## 2 Markdown语法教程

### 2.1 标题

不同数量的`#`可以完成不同的标题，如下：

# 一级标题

## 二级标题

### 三级标题

### 2.2 字体

粗体、斜体、粗体和斜体，删除线，需要在文字前后加不同的标记符号。如下：

**这个是粗体**

*这个是斜体*

***这个是粗体加斜体***

~这里想用删除线~~

注：如果想给字体换颜色、字体或者居中显示，需要使用内嵌HTML来实现。

### 2.3 无序列表

无序列表的使用，在符号`-`后加空格使用。如下：

- 无序列表 1
- 无序列表 2
- 无序列表 3

如果要控制列表的层级，则需要在符号`-`前使用空格。如下：

- 无序列表 1
- 无序列表 2
  - 无序列表 2.1
  - 无序列表 2.2

**由于微信原因，最多支持到二级列表**。

### 2.4 有序列表

有序列表的使用，在数字及符号`.`后加空格后输入内容，如下：

1. 有序列表 1
2. 有序列表 2
3. 有序列表 3

### 2.5 引用

引用的格式是在符号`>`后面书写文字。如下：

> 读一本好书，就是在和高尚的人谈话。 ——歌德

> 雇用制度对工人不利，但工人根本无力摆脱这个制度。 ——阮一峰

### 2.7 链接

微信公众号仅支持公众号文章链接，即域名为`https://mp.weixin.qq.com/`的合法链接。使用方法如下所示：

对于该论述，欢迎读者查阅之前发过的文章，[你是《未来世界的幸存者》么？](https://mp.weixin.qq.com/s/s5IhxV2ooX3JN_X416nidA)
<a id="jump_8"></a>
### 2.8 图片

插入图片，格式如下：

![这里写图片描述](https://www.nginx.cn/wp-content/uploads/2020/03/qrcode_for_gh_82cf87d482f0_258.jpg)

支持 jpg、png、gif、svg 等图片格式，**其中 svg 文件仅可在微信公众平台中使用**，svg 文件示例如下：

![](https://markdown.com.cn/images/i-am-svg.svg)

支持图片**拖拽和截图粘贴**到编辑器中。

注：支持图片 ***拖拽和截图粘贴*** 到编辑器中，仅支持 https 的图片，图片粘贴到微信时会自动上传微信服务器。

### 2.9 分割线

可以在一行中用三个以上的减号来建立一个分隔线，同时需要在分隔线的上面空一行。如下：

---

### 2.10 表格

可以使用冒号来定义表格的对齐方式，如下：

| 姓名   | 年龄 |     工作 |
| :----- | :--: | -------: |
| 小可爱 |  18  | 吃可爱多 |
| 小小勇敢 |  20  | 爬棵勇敢树 |
| 小小小机智 |  22  | 看一本机智书 |



## 3. 特殊语法

### 3.1 脚注

> 支持平台：微信公众号、知乎。

脚注与链接的区别如下所示：

```markdown
链接：[文字](链接)
脚注：[文字](脚注解释 "脚注名字")
```

有人认为在[大前端时代](https://en.wikipedia.org/wiki/Front-end_web_development "Front-end web development")的背景下，移动端开发（Android、IOS）将逐步退出历史舞台。

[全栈工程师](是指掌握多种技能，并能利用多种技能独立完成产品的人。 "什么是全栈工程师")在业务开发流程中起到了至关重要的作用。

脚注内容请拉到最下面观看。

### 3.2 代码块

> 支持平台：微信代码主题仅支持微信公众号！其他主题无限制。

如果在一个行内需要引用代码，只要用反引号引起来就好，如下：

Use the `printf()` function.

在需要高亮的代码块的前一行及后一行使用三个反引号，同时**第一行反引号后面表示代码块所使用的语言**，如下：

```java
// FileName: HelloWorld.java
public class HelloWorld {
  // Java 入口程序，程序从此入口
  public static void main(String[] args) {
    System.out.println("Hello,World!"); // 向控制台打印一条语句
  }
}
```

支持以下语言种类：

```
bash
clojure，cpp，cs，css
dart，dockerfile, diff
erlang
go，gradle，groovy
haskell
java，javascript，json，julia
kotlin
lisp，lua
makefile，markdown，matlab
objectivec
perl，php，python
r，ruby，rust
scala，shell，sql，swift
tex，typescript
verilog，vhdl
xml
yaml
```

如果想要更换代码高亮样式，可在上方**代码主题**中挑选。

其中**微信代码主题与微信官方一致**，有以下注意事项：

- 带行号且不换行，代码大小与官方一致
- 需要在代码块处标志语言，否则无法高亮
- 粘贴到公众号后，用鼠标点代码块内外一次，完成高亮

diff 不能同时和其他语言的高亮同时显示，且需要调整代码主题为微信代码主题以外的代码主题才能看到 diff 效果，使用效果如下:

```diff
+ 新增项
- 删除项
```

**其他主题不带行号，可自定义是否换行，代码大小与当前编辑器一致**

### 3.3 数学公式

> 支持平台：微信公众号、知乎。

行内公式使用方法，比如这个化学公式：$\ce{Hg^2+ ->[I-] HgI2 ->[I-] [Hg^{II}I4]^2-}$

块公式使用方法如下：

$$H(D_2) = -\left(\frac{2}{4}\log_2 \frac{2}{4} + \frac{2}{4}\log_2 \frac{2}{4}\right) = 1$$

矩阵：

$$
  \begin{pmatrix}
  1 & a_1 & a_1^2 & \cdots & a_1^n \\
  1 & a_2 & a_2^2 & \cdots & a_2^n \\
  \vdots & \vdots & \vdots & \ddots & \vdots \\
  1 & a_m & a_m^2 & \cdots & a_m^n \\
  \end{pmatrix}
$$

公式由于微信不支持，目前的解决方案是转成 svg 放到微信中，无需调整，矢量不失真。

目前测试如果公式量过大，在 Chrome 下会存在粘贴后无响应，但是在 Firefox 中始终能够成功。

### 3.4 TOC

> 支持平台：微信公众号、知乎。

TOC 全称为 Table of Content，列出全部标题。

[TOC]

由于微信只支持到二级列表，本工具仅支持二级标题和三级标题的显示。

### 3.5 注音符号

> 支持平台：微信公众号。

支持注音符号，用法如下：

Markdown Nice 这么好用，简直是{喜大普奔|hē hē hē hē}呀！

### 3.6 横屏滑动幻灯片

> 支持平台：微信公众号。

通过`<![](url),![](url)>`这种语法设置横屏滑动滑动片，具体用法如下：

<![蓝1](https://markdown.com.cn/images/blue.jpg),![绿2](https://markdown.com.cn/images/green.jpg),![红3](https://markdown.com.cn.jpg)>

## 4 其他语法

### 4.1 HTML

支持原生 HTML 语法，请写内联样式，如下：

<span style="display:block;text-align:right;color:orangered;">橙色居右</span>
<span style="display:block;text-align:center;color:orangered;">橙色居中</span>

### 4.2 UML

不支持，推荐使用开源工具`https://draw.io/`制作后再导入图片


## 5 致谢

* 歌词经理 [wechat-fromat](https://github.com/lyricat/wechat-format "灵感来源")
* 颜家大少 [MD2All](http://md.aclickall.com/ "MdA2All")

