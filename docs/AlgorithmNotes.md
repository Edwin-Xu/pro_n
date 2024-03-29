# Algorithm Notes

[AlgorithmPDF](_pdf/algorithm/AlgorithmNotes.pdf)

## 基础概念

### 基数

简单来说，基数（cardinality，也译作势），是指一个集合（这里的集合允许存在重复元素，与集合论对集合严格的定义略有不同，如不做特殊说明，本文中提到的集合均允许存在重复元素）中不同元素的个数。例如看下面的集合：



这个集合有9个元素，但是2和3各出现了两次，因此不重复的元素为1,2,3,4,5,9,7，所以这个集合的基数是7。

如果两个集合具有相同的基数，我们说这两个集合等势。基数和等势的概念在有限集范畴内比较直观，但是如果扩展到无限集则会比较复杂，一个无限集可能会与其真子集等势（例如整数集和偶数集是等势的）。不过在这个系列文章中，我们仅讨论有限集的情况，关于无限集合基数的讨论，有兴趣的同学可以参考实变分析相关内容。

容易证明，如果一个集合是有限集，则其基数是一个自然数。



在数据库相关领域也使用基数，比如OLAP中，评估一个列是否是低基数的。



## 常用算法

### 三点共线判断

**已知平面上的三个点A(x1,y1)、B(x2,y2)、C(x3,y3)，求判断它们是否在一条直线上？**

#### 斜率判断

判断**向量AB和向量AC**的斜率是否相等。即：

> (y2 - y1)/(x2 - x1) == (y3 - y1)/(x3 - x1)

为了防止除数为零的问题可以把这个判断转成乘法：

> (y3 - y1) * (x2 - x1) - (y2 - y1) * (x3 - x1)==0

注意：

千万不要把AB、BC的斜率求出来进行判断，因为计算机浮点数都是由误差的，不能精确表达。

如果要求出来，只能用分数形式表达：进行约分，即求出最大公约数k后分子分母同时除以k.

#### 面积判断

**判断三角形ABC的面积S然后判断S是否为0**

可以通过行列式求三角形面积或者海伦公式求三角形面积

行列式： S = (1/2) * (x1 * y2 + x2*y3 + x3 \* y1 - x1 \* y3 - x2 \* y1 - x3 \* y2)*

![image-20220522164254408](_images/AlgorithmNotes.asserts/image-20220522164254408.png)

*海伦公式：S=sqrt(p*(p-a)*(p-b)*(p-c))，其中a,b,c为三角形边长，p=C/2是三角形的半周长







## 位运算

### LSB MSB

**最低有效位**（**the least significant bit**，**lsb**）是指一个二进制数字中的第0位（即最低位），具有权值为2^0，可以用它来检测数的奇偶性。与之相反的称之为最高有效位。在大端序中，lsb指最右边的位



**最高有效位**（**the Most Significant Bit**，**msb**），是指一个n位二进制数字中的n-1位，具有最高的权值2^n − 1。与之相反的称之为最低有效位。在大端序中，msb即指最左端的位。

### xor & (-xor)

取异或值最后一个二进制位为 1 的数字作为 mask，如果是 1 则表示两个数字在这一位上不同。

```java
  int mask = xor & (-xor);
```

### 大小写转换

大写变小写、小写变大写 : 字符 ^= 32;

大写变小写、小写变小写 : 字符 |= 32;

小写变大写、大写变大写 : 字符 &= -33;



## 布隆过滤器

BloomFilter

1970年布隆提出

实际上是一个很长的二进制向量和一系列随机映射函数

主要用于判断一个元素是否在一个集合中



一般判断元素在集合中的方法是利用 树、链表、HASH结构等

但是随着数据量的增加，存储空间需求急剧增加

布隆过滤器就产生了

### hash

哈希函数的概念是：将任意大小的输入数据转换成特定大小的输出数据的函数，转换后的数据称为哈希值或哈希编码，也叫散列值。

所有散列函数都有如下基本特性：

- 如果两个散列值是不相同的（根据同一函数），那么这两个散列值的原始输入也是不相同的。这个特性是散列函数具有确定性的结果，具有这种性质的散列函数称为**单向散列函数**。
- 散列函数的输入和输出不是唯一对应关系的，如果两个散列值相同，两个输入值很可能是相同的，但也可能不同，这种情况称为“**散列碰撞**（collision）”

### 布隆过滤器结构

bloom filter是由一个固定大小的二进制向量或者位图和一系列的映射函数构成的。

在初始状态时，对于长度为 m 的位数组，它的所有位都被置为0

当有变量被加入集合时，通过 K 个映射函数将这个变量映射成位图中的 K 个点，把它们置为 1（假定有两个变量都通过 3 个映射函数）。

![image-20211108001459145](_images/AlgorithmNotes.assets/image-20211108001459145.png)

查询某个变量的时候我们只要看看这些点是不是都是 1 就可以大概率知道集合中有没有它了

- 如果这些点有任何一个 0，则被查询变量一定不在；
- 如果都是 1，则被查询变量很**可能存在**

为什么说是可能存在，而不是一定存在呢？那是因为映射函数本身就是散列函数，散列函数是会有碰撞的

### 误判率

布隆过滤器的误判是指多个输入经过哈希之后在相同的bit位置1了，这样就无法判断究竟是哪个输入产生的，因此误判的根源在于相同的 bit 位被多次映射且置 1。

这种情况也造成了布隆过滤器的删除问题，因为布隆过滤器的每一个 bit 并不是独占的，很有可能多个元素共享了某一位。如果我们直接删除这一位的话，会影响其他的元素。(比如上图中的第 3 位)

### 特性

- **一个元素如果判断结果为存在的时候元素不一定存在，但是判断结果为不存在的时候则一定不存在**。
- **布隆过滤器可以添加元素，但是不能删除元素**。因为删掉元素会导致误判率增加。



### 优点

相比于其它的数据结构，布隆过滤器在空间和时间方面都有巨大的优势。布隆过滤器存储空间和插入/查询时间都是常数 $O(K)$，另外，散列函数相互之间没有关系，方便由硬件并行实现。布隆过滤器不需要存储元素本身，在某些对保密要求非常严格的场合有优势。

布隆过滤器可以表示全集，其它任何数据结构都不能；

### 缺点

但是布隆过滤器的缺点和优点一样明显。误算率是其中之一。随着存入的元素数量增加，误算率随之增加。但是如果元素数量太少，则使用散列表足矣。

另外，一般情况下不能从布隆过滤器中删除元素。我们很容易想到把位数组变成整数数组，每插入一个元素相应的计数器加 1, 这样删除元素时将计数器减掉就可以了。然而要保证安全地删除元素并非如此简单。首先我们必须保证删除的元素的确在布隆过滤器里面。这一点单凭这个过滤器是无法保证的。另外计数器回绕也会造成问题。

### 布隆过滤器的典型应用有：

- 数据库防止穿库。 Google Bigtable，HBase 和 Cassandra 以及 Postgresql 使用BloomFilter来减少不存在的行或列的磁盘查找。避免代价高昂的磁盘查找会大大提高数据库查询操作的性能。
- 业务场景中判断用户是否阅读过某视频或文章，比如抖音或头条，当然会导致一定的误判，但不会让用户看到重复的内容。
- 缓存宕机、缓存击穿场景，一般判断用户是否在缓存中，如果在则直接返回结果，不在则查询db，如果来一波冷数据，会导致缓存大量击穿，造成雪崩效应，这时候可以用布隆过滤器当缓存的索引，只有在布隆过滤器中，才去查询缓存，如果没查询到，则穿透到db。如果不在布隆器中，则直接返回。
- WEB拦截器，如果相同请求则拦截，防止重复被攻击。用户第一次请求，将请求参数放入布隆过滤器中，当第二次请求时，先判断请求参数是否被布隆过滤器命中。可以提高缓存命中率。Squid 网页代理缓存服务器在 cache digests 中就使用了布隆过滤器。Google Chrome浏览器使用了布隆过滤器加速安全浏览服务
- Venti 文档存储系统也采用布隆过滤器来检测先前存储的数据。
- SPIN 模型检测器也使用布隆过滤器在大规模验证问题时跟踪可达状态空间。



```java
public class MyBloomFilter {

    /**
     * 一个长度为10 亿的比特位
     */
    private static final int DEFAULT_SIZE = 256 << 22;

    /**
     * 为了降低错误率，使用加法hash算法，所以定义一个8个元素的质数数组
     */
    private static final int[] seeds = {3, 5, 7, 11, 13, 31, 37, 61};

    /**
     * 相当于构建 8 个不同的hash算法
     */
    private static HashFunction[] functions = new HashFunction[seeds.length];

    /**
     * 初始化布隆过滤器的 bitmap
     */
    private static BitSet bitset = new BitSet(DEFAULT_SIZE);

    /**
     * 添加数据
     *
     * @param value 需要加入的值
     */
    public static void add(String value) {
        if (value != null) {
            for (HashFunction f : functions) {
                //计算 hash 值并修改 bitmap 中相应位置为 true
                bitset.set(f.hash(value), true);
            }
        }
    }

    /**
     * 判断相应元素是否存在
     * @param value 需要判断的元素
     * @return 结果
     */
    public static boolean contains(String value) {
        if (value == null) {
            return false;
        }
        boolean ret = true;
        for (HashFunction f : functions) {
            ret = bitset.get(f.hash(value));
            //一个 hash 函数返回 false 则跳出循环
            if (!ret) {
                break;
            }
        }
        return ret;
    }

    /**
     * 模拟用户是不是会员，或用户在不在线。。。
     */
    public static void main(String[] args) {

        for (int i = 0; i < seeds.length; i++) {
            functions[i] = new HashFunction(DEFAULT_SIZE, seeds[i]);
        }

        // 添加1亿数据
        for (int i = 0; i < 100000000; i++) {
            add(String.valueOf(i));
        }
        String id = "123456789";
        add(id);

        System.out.println(contains(id));   // true
        System.out.println("" + contains("234567890"));  //false
    }
}

class HashFunction {

    private int size;
    private int seed;

    public HashFunction(int size, int seed) {
        this.size = size;
        this.seed = seed;
    }

    public int hash(String value) {
        int result = 0;
        int len = value.length();
        for (int i = 0; i < len; i++) {
            result = seed * result + value.charAt(i);
        }
        int r = (size - 1) & result;
        return (size - 1) & result;
    }
}
```

## 数组相关

### 前缀和

**前缀和主要适用的场景是原始数组不会被修改的情况下，频繁查询某个区间的累加和**

### 差分数组

差分其实就是数据之间的差，什么数据的差呢？就是**上面所给的原始数组的相邻元素之间的差值**，我们令 ***\*d[i]=a[i+1]-a[i]\****，一遍for循环即可将差分数组求出来。

差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减

使用场景：对于一个数组 nums[]

要求一：对 num[2...4] 全部 + 1
要求二：对 num[1...3] 全部 - 3
要求三：对 num[0...4] 全部 + 9

看到上述情景，首先想到的肯定是遍历（bao li）。直接对数组循环 3 遍，每次在规定的区间上按要求进行操作，此时时间复杂度 O(3n)

但是当这样的操作变得频繁后，时间复杂度也呈线性递增

所以针对这种场景，提出了「差分数组」的概念，举个简单的例子

![1036101649298970IqiB0Himage-20220407103610070.png](_images/AlgorithmNotes.asserts/1649302054-sjfBPU-1036101649298970IqiB0Himage-20220407103610070.png)

当我们需要对 nums[] 进行上述三个要求时，不需要一次一次的遍历整个数组了，而只需要对 diff[] 进行一次 O(1) 的操作即可

要求一：diff[2] += 1;
要求二：diff[1] += (-3); diff[3 + 1] -= (-3);
要求三：diff[0] += 9;

总结：**对于改变区间 [i, j] 的值，只需要进行如下操作 diff[i] += val; diff[j + 1] -= val**

注：当 j >= diff.length 时，不需要进行 diff[j + 1] -= val 操作

**当你将原始数组中元素同时加上或者减掉某个数，那么他们的差分数组其实是不会变化的。**



怎么通过 `diff[]` 得到更新后的数组呢？

```
// 复原操作
int[] res = new int[n];
// 下标为 0 的元素相等
res[0] = diff[0];
for (int i = 1; i < n; i++) {
    res[i] = diff[i] + res[i - 1];
}
```



`diff[]` 原理:

当我们需要对区间 [i, j] 进行 + val 操作时，我们对 diff[i] += val; diff[j + 1] -= val;

在复原操作时，当我们求 res[i] 时，res[i - 1] 没有变，而 diff[i] 增加了 3，所以 res[i] 增加 3

当我们求 res[i + 1] 时，res[i] 增加了 3，而 diff[i + 1] 没有变，故 res[i + 1] = diff[i + 1] + res[i] 增加 3。即：虽然 diff[i + 1] 没有变，但是 res[i] 对后面的 res[i + 1] 有一个累积作用

当我们求 res[j + 1] 时，res[j] 增加了 3，而 diff[j + 1] 减少了 3，故 res[j + 1] = diff[j + 1] + res[j] 增加没有变。即:我们在 j + 1 的时候，把上述的累积作用去除了，所以 j + 1 后面的元素不受影响

```sql
public class Difference {

    /**
     * 差分数组
     */
    private final int[] diff;

    /**
     * 初始化差分数组
     * @param nums nums
     */
    public Difference(int[] nums) {
        assert nums.length > 0;
        diff = new int[nums.length];
        diff[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i - 1];
        }
    }

    /**
     * 对区间 [i, j] 增加 val（val 可为负数）
     * @param i i
     * @param j j
     * @param val val
     */
    public void increment(int i, int j, int val) {
        diff[i] += val;
        if (j + 1 < diff.length) {
            diff[j + 1] -= val;
        }
    }

    /**
     * 复原操作
     * @return res
     */
    public int[] result() {
        int[] res = new int[diff.length];
        res[0] = diff[0];
        for (int i = 1; i < diff.length; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```



例子：

1、将区间【1，4】的数值全部加上3

2、将区间【3，5】的数值全部减去5

**当你将原始数组中元素同时加上或者减掉某个数，那么他们的差分数组其实是不会变化的。**

![img](_images/AlgorithmNotes.asserts/20190825105034307.PNG)

利用这个思想，咱们将区间缩小，缩小的例子中的区间 【1,4】吧这是你会发现只有 d[1]和d[5]发生了变化，而d[2],d[3],d[4]却保持着原样，

![img](_images/AlgorithmNotes.asserts/2019082510504048.PNG)

这时我们就会发现这样一个规律，当**对一个区间进行增减某个值的时候，他的差分数组对应的区间左端点的值会同步变化，而他的右端点的后一个值则会相反地变化**，其实这个很好理解

因为我们的差分数组是由原始数组的相邻两项作差求出来的，即 d[i]=a[i]-a[i-1]；那么我们能不能反过来，求得一下修改过后的a[i]呢？

**\*直接反过来即得 a[i]=a[i-1]+d[i]\***



### 并查集







## 树

### 字典树

字典树 Tire

字典树也叫Trie树、前缀树。顾名思义，它是一种针对字符串进行维护的数据结构。

字典树，顾名思义，是关于“字典”的一棵树。即：它是对于字典的一种存储方式（所以是一种数据结构而不是算法）。这个词典中的每个“单词”就是从根节点出发一直到某一个目标节点的路径，路径中每条边的字母连起来就是一个单词



字典树的本质是把很多字符串拆成单个字符的形式，以树的方式存储起来

那么根据这个最基本的性质，我们可以由此延伸出字典树的很多妙用。简单总结起来大体如下：

- 1、维护字符串集合（即**字典**）。
- 2、向字符串集合中插入字符串（即**建树**）。
- 3、查询字符串集合中是否有某个字符串（即**查询**）。
- 4、统计字符串在集合中出现的个数（即**统计**）。
- 5、将字符串集合按字典序排序（即**字典序排序**）。
- 6、求集合内两个字符串的LCP（Longest Common Prefix，最长公共前缀）（即**求最长公共前缀**）。

经常被搜索引擎系统用于文本词频统计。它的优点是：最大限度地减少无谓的字符串比较。

Trie的核心思想是空间换时间。利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。

**前缀树的3个基本性质：**

1. 根节点不包含字符，除根节点外每一个节点都只包含一个字符。
2. 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
3. 每个节点的所有子节点包含的字符都不相同。
4. 它的key都为字符串，能做到高效查询和插入，时间复杂度为O(k)，k为字符串长度，缺点是如果大量字符串没有共同前缀时很耗内存。



```java
其中count表示以当前单词结尾的单词数量。
prefix表示以该处节点之前的字符串为前缀的单词数量。
    
public class TrieNode {
	int count;
	int prefix;
	TrieNode[] nextNode=new TrieNode[26];
	public TrieNode(){
		count=0;
		prefix=0;
	}
}

public static void insert(TrieNode root,String str){
    if(root==null||str.length()==0){
        return;
    }
    char[] c=str.toCharArray();
    for(int i=0;i<str.length();i++){
        //如果该分支不存在，创建一个新节点
        if(root.nextNode[c[i]-'a']==null){
            root.nextNode[c[i]-'a']=new TrieNode();
        }
        root=root.nextNode[c[i]-'a'];
        root.prefix++;//注意，应该加在后面
    }

    //以该节点结尾的单词数+1
    root.count++;
}

public static int search(TrieNode root,String str){
    if(root==null||str.length()==0){
        return -1;
    }
    char[] c=str.toCharArray();
    for(int i=0;i<str.length();i++){
        //如果该分支不存在，表名该单词不存在
        if(root.nextNode[c[i]-'a']==null){
            return -1;
        }
        //如果存在，则继续向下遍历
        root=root.nextNode[c[i]-'a'];	
    }

    //如果count==0,也说明该单词不存在
    if(root.count==0){
        return -1;
    }
    return root.count;
}

//查询以str为前缀的单词数量
public static int searchPrefix(TrieNode root,String str){
    if(root==null||str.length()==0){
        return -1;
    }
    char[] c=str.toCharArray();
    for(int i=0;i<str.length();i++){
        //如果该分支不存在，表名该单词不存在
        if(root.nextNode[c[i]-'a']==null){
            return -1;
        }
        //如果存在，则继续向下遍历
        root=root.nextNode[c[i]-'a'];	
    }
    return root.prefix;
}
```

### 线段树



## 随机算法

### 水塘抽样

大数据流中的随机抽样问题，即：**当内存无法加载全部数据时，如何从包含未知大小的数据流中随机选取k个数据，并且要保证每个数据被抽取到的概率相等。**



#### k=1

首先考虑简单的情况，**当**k=1时，如何制定策略：

假设数据流含有N个数，我们知道如果要保证所有的数被抽到的概率相等，那么每个数抽到的概率应该为 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7BN%7D) 。

那我们可以这样做：

- 遇到第1个数 ![[公式]](https://www.zhihu.com/equation?tex=n_1) 的时候，我们保留它， ![[公式]](https://www.zhihu.com/equation?tex=p%28n_1%29%3D1)
- 遇到第2个数 ![[公式]](https://www.zhihu.com/equation?tex=n_2) 的时候，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7B2%7D) 的概率保留它，那么 ![[公式]](https://www.zhihu.com/equation?tex=p%28n_1%29%3D1%5Ctimes+%5Cfrac%7B1%7D%7B2%7D%3D%5Cfrac%7B1%7D%7B2%7D) ，![[公式]](https://www.zhihu.com/equation?tex=p%28n_2%29%3D%5Cfrac%7B1%7D%7B2%7D)
- 遇到第3个数 ![[公式]](https://www.zhihu.com/equation?tex=n_3) 的时候，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7B3%7D) 的概率保留它，那么 ![[公式]](https://www.zhihu.com/equation?tex=p%28n_1%29%3Dp%28n_2%29%3D%5Cfrac%7B1%7D%7B2%7D%5Ctimes%281-%5Cfrac%7B1%7D%7B3%7D%29%3D%5Cfrac%7B1%7D%7B3%7D) ， ![[公式]](https://www.zhihu.com/equation?tex=p%28n_3%29%3D%5Cfrac%7B1%7D%7B3%7D)
- ……
- 遇到第i个数 ![[公式]](https://www.zhihu.com/equation?tex=n_i) 的时候，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7Bi%7D) 的概率保留它，那么 ![[公式]](https://www.zhihu.com/equation?tex=p%28n_1%29%3Dp%28n_2%29%3Dp%28n_3%29%3D%5Cdots%3Dp%28n_%7Bi-1%7D%29%3D%5Cfrac%7B1%7D%7Bi-1%7D%5Ctimes%281-%5Cfrac%7B1%7D%7Bi%7D%29%3D%5Cfrac%7B1%7D%7Bi%7D) ， ![[公式]](https://www.zhihu.com/equation?tex=p%28n_i%29%3D%5Cfrac%7B1%7D%7Bi%7D)

这样就可以看出，对于k=1的情况，我们可以制定这样简单的抽样策略：

*数据流中第i个数被保留的概率为 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7Bi%7D) 。只要采取这种策略，只需要遍历一遍数据流就可以得到采样值，并且保证所有数被选取的概率均为 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7BN%7D) 。*

#### k>1

对于k>1的情况，我们可以采用类似的思考策略：

仍然假设数据流中含有N个数，那么要保证所有的数被抽到的概率相等，每个数被选取的概率必然为 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7Bk%7D%7BN%7D) 。

- 对于前k个数 ![[公式]](https://www.zhihu.com/equation?tex=n_1%2Cn_2%2C%5Cdots%2Cn_k) ，我们保留下来，则 ![[公式]](https://www.zhihu.com/equation?tex=p%28n_1%29%3Dp%28n_2%29%3D%5Cdots%3Dp%28n_k%29%3D1) （下面连等采用 ![[公式]](https://www.zhihu.com/equation?tex=p%28n_%7B1%3Ak%7D%29) 的形式）
- 对于第k+1个数 ![[公式]](https://www.zhihu.com/equation?tex=n_%7Bk%2B1%7D) ，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7Bk%7D%7Bk%2B1%7D) 的概率保留它（这里只是指本次被保留下来），那么前k个数中的 ![[公式]](https://www.zhihu.com/equation?tex=n_r%28r%5Cin%7B1%3Ak%7D%29) 被保留的概率可以这样表示： ![[公式]](https://www.zhihu.com/equation?tex=p%28n_r%E8%A2%AB%E4%BF%9D%E7%95%99%29%3Dp%28%E4%B8%8A%E4%B8%80%E8%BD%AEn_r%E8%A2%AB%E4%BF%9D%E7%95%99%29%5Ctimes%28p%28n_%7Bk%2B1%7D%E8%A2%AB%E4%B8%A2%E5%BC%83%29%2Bp%28n_%7Bk%2B1%7D%E8%A2%AB%E4%BF%9D%E7%95%99%29%5Ctimes+p%28n_r%E6%9C%AA%E8%A2%AB%E6%9B%BF%E6%8D%A2%29%29) ，即 ![[公式]](https://www.zhihu.com/equation?tex=p_%7B1%3Ak%7D%3D%5Cfrac%7B1%7D%7Bk%2B1%7D%2B%5Cfrac%7Bk%7D%7Bk%2B1%7D%5Ctimes+%5Cfrac%7Bk-1%7D%7Bk%7D%3D%5Cfrac%7Bk%7D%7Bk%2B1%7D)
- 对于第k+2个数 ![[公式]](https://www.zhihu.com/equation?tex=n_%7Bk%2B2%7D) ，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7Bk%7D%7Bk%2B2%7D) 的概率保留它（这里只是指本次被保留下来），那么前k个被保留下来的数中的 ![[公式]](https://www.zhihu.com/equation?tex=n_r%28r%5Cin%7B1%3Ak%7D%29) 被保留的概率为 ![[公式]](https://www.zhihu.com/equation?tex=p_%7B1%3Ak%7D%3D%5Cfrac%7Bk%7D%7Bk%2B1%7D%5Ctimes%28%5Cfrac%7B2%7D%7Bk%2B2%7D%2B%5Cfrac%7Bk%7D%7Bk%2B2%7D%5Ctimes+%5Cfrac%7Bk-1%7D%7Bk%7D%29%3D%5Cfrac%7Bk%7D%7Bk%2B1%7D%5Ctimes%5Cfrac%7Bk%2B1%7D%7Bk%2B2%7D%3D%5Cfrac%7Bk%7D%7Bk%2B2%7D)
- ……
- 对于第i（i>k）个数 ![[公式]](https://www.zhihu.com/equation?tex=n_i) ，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7Bk%7D%7Bi%7D) 的概率保留它，前i-1个数中的 ![[公式]](https://www.zhihu.com/equation?tex=n_r%28r%5Cin%7B1%3Ai-1%7D%29) 被保留的概率为 ![[公式]](https://www.zhihu.com/equation?tex=p_%7B1%3Ak%7D%3D%5Cfrac%7Bk%7D%7Bi-1%7D%5Ctimes+%28%5Cfrac%7Bi-k%7D%7Bi%7D%2B%5Cfrac%7Bk%7D%7Bi%7D%5Ctimes+%5Cfrac%7Bk-1%7D%7Bk%7D%29%3D%5Cfrac%7Bk%7D%7Bi-1%7D%5Ctimes%5Cfrac%7Bi-1%7D%7Bi%7D%3D%5Cfrac%7Bk%7D%7Bi%7D)

这样，我们可以制订策略：

**对于前k个数，我们全部保留，对于第i（i>k）个数，我们以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7Bk%7D%7Bi%7D) 的概率保留第i个数，并以 ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7Bk%7D) 的概率与前面已选择的k个数中的任意一个替换。**



应用：

https://leetcode-cn.com/problems/random-pick-index/solution/sui-ji-shu-suo-yin-by-leetcode-solution-ofsq/



## labuladong

### 核心框架汇总

#### 框架思维

##### 数据结构的存储方式

**数据结构的存储方式只有两种：数组（顺序存储）和链表（链式存储）**

散列表、栈、队列、堆、树、图？

数组和链表才是「结构基础」

那些多样化的数据结构，究其源头，都是在链表或者数组上的特殊操作，API 不同而已。

比如说「队列」、「栈」这两种数据结构既可以使用链表也可以使用数组实现。用数组实现，就要处理扩容缩容的问题；用链表实现，没有这个问题，但需要更多的内存空间存储节点指针

数据结构种类很多，甚至你也可以发明自己的数据结构，但是底层存储无非数组或者链表，**二者的优缺点如下**：

**数组**由于是**紧凑连续存储,可以随机访问**，通过**索引**快速找到对应元素，而且**相对节约存储空间**。但正因为连续存储，**内存空间必须一次性分配够，所以说数组如果要扩容**，**需要重新分配一块更大的空间，再把数据全部复制过去，时间复杂度 O(N)**；而且你如果想在数组中间进行**插入和删除，每次必须搬移后面的所有数据以保持连续，时间复杂度 O(N)**。

**链表**因为元素不连续，而是靠**指针**指向下一个元素的位置，所以不存在数组的扩容问题；如果知道某一元素的前驱和后驱，操作指针即可删除该元素或者插入新元素，时间复杂度 O(1)。但是正因为存储空间不连续，你无法根据一个索引算出对应元素的地址，所以**不能随机访问**；而且由于每个元素必须存储指向前后元素位置的指针，会**消耗相对更多的储存空间**。

##### 数据结构的基本操作

对于任何数据结构，其基本操作无非遍历 + 访问，再具体一点就是：**增删查改**。

**数据结构种类很多，但它们存在的目的都是在不同的应用场景，尽可能高效地增删查改**

种数据结构的遍历 + 访问无非两种形式：**线性的和非线性**的。

线性就是 for/while 迭代为代表，非线性就是递归为代表

**所谓框架，就是套路。不管增删查改，这些代码都是永远无法脱离的结构**，你可以把这个结构作为大纲，根据具体问题在框架上添加代码就行了

##### 算法刷题指南

**数据结构是工具，算法是通过合适的工具解决特定问题的方法**

刷题顺序：

- **先学习像数组、链表这种基本数据结构的常用算法**
- **学会基础算法之后，不要急着上来就刷回溯算法、动态规划这类笔试常考题，而应该先刷二叉树，先刷二叉树，先刷二叉树**
- 

**这就是框架的力量，能够保证你在快睡着的时候，依然能写出正确的程序；就算你啥都不会，都能比别人高一个级别**。



<u>**数据结构的基本存储方式就是链式和顺序两种，基本操作就是增删查改，遍历方式无非迭代和递归。**</u>





> 学习心得：学习算法还是从基础实施出发， 基础数据结构 就是 数据+链表 两种，数据结构上的操作就是增删改查+遍历，处理方式包含线性与非线性，即递归与迭代。
>
> 数据结构是基础工具，算法就是利用工具，解决在特定场景下的增删改查问题。
>
> 学习算法先从 数据、链表出发，然后是树。



#### 刷题心得

##### 算法的本质

**我想说算法的本质就是「穷举」**

**「算法工程师」做的这个「算法」，和「数据结构与算法」中的这个「算法」完全是两码事**

对前者来说，重点在数学建模和调参经验，计算机真就只是拿来做计算的工具而已；而后者的重点是计算机思维，需要你能够站在计算机的视角，抽象、化简实际问题，然后用合理的数据结构去解决问题。

**为了区分，不妨称算法工程师研究的算法为「数学算法」，称刷题面试的算法为「计算机算法」，我写的内容主要聚焦的是「计算机算法」**

其实计算机思维也没什么高端的，你想想计算机的特点是啥？不就是快嘛，你的脑回路一秒只能转一圈，人家 CPU 转几万圈无压力。所以计算机解决问题的方式大道至简，就是穷举。

**千万不要觉得穷举这个事儿很简单，穷举有两个关键难点：无遗漏、无冗余**。

遗漏，会直接导致答案出错；冗余，会拖慢算法的运行速度

所以，当你看到一道算法题，可以从这两个维度去思考：

**1、如何穷举**？即无遗漏地穷举所有可能解。

**2、如何聪明地穷举**？即避免所有冗余的计算，消耗尽可能少的资源求出答案。

##### 数组/单链表系列算法

**单链表常考的技巧就是双指针**

比如判断单链表是否成环，拍脑袋的暴力解是什么？就是用一个 `HashSet` 之类的数据结构来缓存走过的节点，遇到重复的就说明有环对吧。但我们用快慢指针可以避免使用额外的空间，这就是聪明地穷举嘛。

**数组常用的技巧有很大一部分还是双指针相关的技巧，说白了是教你如何聪明地进行穷举**。

**首先说二分搜索技巧**，可以归为两端向中心的双指针。如果让你在数组中搜索元素，一个 for 循环穷举肯定能搞定对吧，但如果数组是有序的，二分搜索不就是一种更聪明的搜索方式么。

**再说说 [滑动窗口算法技巧](https://labuladong.github.io/algo/2/20/27/)**，典型的快慢双指针，快慢指针中间就是滑动的「窗口」，主要用于解决子串问题。

**最后说说 [前缀和技巧](https://labuladong.github.io/algo/2/20/24/) 和 [差分数组技巧](https://labuladong.github.io/algo/2/20/25/)**。

如果频繁地让你计算子数组的和，每次用 for 循环去遍历肯定没问题，但前缀和技巧预计算一个 `preSum` 数组，就可以避免循环。

类似的，如果频繁地让你对子数组进行增减操作，也可以每次用 for 循环去操作，但差分数组技巧维护一个 `diff` 数组，也可以避免循环。



在做题的时候要思考，联想，进而培养举一反三的能力。

#### 双指针技巧秒杀七道链表题目

单链表的基本技巧：

1、合并两个有序链表

2、链表的分解

3、合并 `k` 个有序链表

4、寻找单链表的倒数第 `k` 个节点

5、寻找单链表的中点

6、判断单链表是否包含环并找出环起点

7、判断两个单链表是否相交并找出交点

##### 合并两个有序链表

**「虚拟头结点」技巧，也就是 `dummy` 节点**。你可以试试，如果不使用 `dummy` 虚拟节点，代码会复杂很多，而有了 `dummy` 节点这个占位符，可以避免处理空指针的情况，降低代码的复杂性。

##### 单链表的分解

##### 合并 k 个有序链表

- 利用最小堆
- 归并方式：每次合并两条

##### 单链表的倒数第 k 个节点

笨方法： 遍历两次

能只需要遍历一次吗？ 可以，两个指针：

第一个指针先走k步，然后第二个指针指向头部，两个指针同时走，第1个指针到最后，第二个刚好到 倒数 k个



还有一种方法： 递归，递归具有反向的效果

##### 单链表的中点

「快慢指针」

每当慢指针 `slow` 前进一步，快指针 `fast` 就前进两步，这样，当 `fast` 走到链表末尾时，`slow` 就指向了链表中点



##### 判断链表是否包含环

如果链表中含有环，如何计算这个环的起点、长度

龟兔赛跑 算法



#### 双指针秒杀七道数组题目

##### 快慢指针技巧

双指针技巧主要分为两类：**左右指针**和**快慢指针**。

所谓左右指针，就是两个指针相向而行或者相背而行；而所谓快慢指针，就是两个指针同向而行，一快一慢。

> 给你一个 **升序排列** 的数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序** 应该保持 **一致** 。

如果每次找到一个不重复的元素都把后面的元素往前移动，时间复杂度高，使用 **快慢指针** 更适合：

```java
    public int removeDuplicates(int[] nums) {
        int slow = 0, fast = 0;
        int len = nums.length;
        long prev = Long.MIN_VALUE;
        while(fast < len){
            while(fast<len && nums[fast] == prev){
                fast++;
            }
            if(fast >= len)break;
            prev = nums[fast];
            if(fast<len)nums[slow++] = nums[fast];
        }
        return slow;
    }

// 更好：
int removeDuplicates(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }
    int slow = 0, fast = 0;
    while (fast < nums.length) {
        if (nums[fast] != nums[slow]) {
            slow++;
            // 维护 nums[0..slow] 无重复
            nums[slow] = nums[fast];
        }
        fast++;
    }
    // 数组长度为索引 + 1
    return slow + 1;
}
```

##### 左右指针的常用算法

###### 二分查找

```java
int binarySearch(int[] nums, int target) {
    // 一左一右两个指针相向而行
    int left = 0, right = nums.length - 1;
    while(left <= right) {
        int mid = (right + left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; 
        else if (nums[mid] > target)
            right = mid - 1;
    }
    return -1;
}
```

###### 回文判断

回文串判断

[最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

#### 二叉树-纲领篇

二叉树解题的思维模式分两类：

**1、是否可以通过遍历一遍二叉树得到答案**？如果可以，用一个 `traverse` 函数配合外部变量来实现，这叫「遍历」的思维模式。

**2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案**？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫「分解问题」的思维模式。

**如果单独抽出一个二叉树节点，它需要做什么事情？需要在什么时候（前/中/后序位置）做**？

##### 二叉树的重要性

举个例子，比如两个经典排序算法 [快速排序](https://labuladong.github.io/algo/2/21/45/) 和 [归并排序](https://labuladong.github.io/algo/2/21/41/)，对于它俩，你有什么理解？

**如果你告诉我，快速排序就是个二叉树的前序遍历，归并排序就是个二叉树的后序遍历，那么我就知道你是个算法高手了**

快速排序的逻辑是，若要对 `nums[lo..hi]` 进行排序，我们先找一个分界点 `p`，通过交换元素使得 `nums[lo..p-1]` 都小于等于 `nums[p]`，且 `nums[p+1..hi]` 都大于 `nums[p]`，然后递归地去 `nums[lo..p-1]` 和 `nums[p+1..hi]` 中寻找新的分界点，最后整个数组就被排序了。

```
void sort(int[] nums, int lo, int hi) {
    /****** 前序遍历位置 ******/
    // 通过交换元素构建分界点 p
    int p = partition(nums, lo, hi);
    /************************/

    sort(nums, lo, p - 1);
    sort(nums, p + 1, hi);
}
```

归并排序的逻辑，若要对 `nums[lo..hi]` 进行排序，我们先对 `nums[lo..mid]` 排序，再对 `nums[mid+1..hi]` 排序，最后把这两个有序的子数组合并，整个数组就排好序了。

归并排序的代码框架如下：

```java
// 定义：排序 nums[lo..hi]
void sort(int[] nums, int lo, int hi) {
    int mid = (lo + hi) / 2;
    // 排序 nums[lo..mid]
    sort(nums, lo, mid);
    // 排序 nums[mid+1..hi]
    sort(nums, mid + 1, hi);

    /****** 后序位置 ******/
    // 合并 nums[lo..mid] 和 nums[mid+1..hi]
    merge(nums, lo, mid, hi);
    /*********************/
}
```

##### 深入理解前中后序

```java
void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // 前序位置
    traverse(root.left);
    // 中序位置
    traverse(root.right);
    // 后序位置
}
```

**前中后序是遍历二叉树过程中处理每一个节点的三个特殊时间点**

##### 两种解题思路

**二叉树题目的递归解法可以分两类思路，第一类是遍历一遍二叉树得出答案，第二类是通过分解问题计算出答案，这两类思路分别对应着 [回溯算法核心框架](https://labuladong.github.io/algo/4/31/105/) 和 [动态规划核心框架](https://labuladong.github.io/algo/3/25/69/)**。

 [二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/):

- 遍历思路：定义外部计数变量，递归或者层序遍历，更新计数
- 递归：递归计数



前序位置的代码执行是自顶向下的，而后序位置的代码执行是自底向上的



二叉树最大直径：

```
 public int diameterOfBinaryTree(TreeNode root) {
        traverse(root);
        return max;
    }

    int max=-1;

    int traverse(TreeNode root){
        if (root == null) return 0;
        int  left = traverse(root.left);
        int  right = traverse(root.right) ;
        max =  Math.max(right+left, max);
        return  Math.max(left, right)+1;
    }
```

#### 动态规划解题套路框架

**动态规划问题的一般形式就是求最值**。动态规划其实是**运筹学**的一种最优化方法，只不过在计算机问题上应用比较多，比如说让你求最长递增子序列呀，最小编辑距离呀等等。

**求解动态规划的核心问题是穷举**

只有列出**正确的「状态转移方程」**，才能正确地穷举

需要判断算法问题是否**具备「最优子结构」**，是否能够通过子问题的最值得到原问题的最值。另外，动态规划问题**存在「重叠子问题」**，如果暴力穷举的话效率会很低，所以需要你使用「**备忘录」或者「DP table」**来优化穷举过程，避免不必要的计算

**重叠子问题、最优子结构、状态转移方程就是动态规划三要素**

思维框架:

**明确 base case -> 明确「状态」-> 明确「选择」 -> 定义 `dp` 数组/函数的含义**。

```python
# 自顶向下递归的动态规划
def dp(状态1, 状态2, ...):
    for 选择 in 所有可能的选择:
        # 此时的状态已经因为做了选择而改变
        result = 求最值(result, dp(状态1, 状态2, ...))
    return result

# 自底向上迭代的动态规划
# 初始化 base case
dp[0][0][...] = base case
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```

「自顶向下」进行「递归」求解，我们更常见的动态规划代码是「自底向上」进行「递推」

##### 斐波那契

```java
// 递归-自顶向下
    public int fib(int n) {
        if(n <=0) return 0;
        else if(n==1) return 1;
        else return fib(n-1) + fib(n-2);
    }
// 递归-自顶向下-备忘录
int fib(int N) {
    // 备忘录全初始化为 0
    int[] memo = new int[N + 1];
    // 进行带备忘录的递归
    return helper(memo, N);
}

int helper(int[] memo, int n) {
    // base case
    if (n == 0 || n == 1) return n;
    // 已经计算过，不用再计算了
    if (memo[n] != 0) return memo[n];
    memo[n] = helper(memo, n - 1) + helper(memo, n - 2);
    return memo[n];
}
// 自底向上
int fib(int N) {
    if (N == 0) return 0;
    int[] dp = new int[N + 1];
    // base case
    dp[0] = 0; dp[1] = 1;
    // 状态转移
    for (int i = 2; i <= N; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[N];
}

int fib(int n) {
    if (n == 0 || n == 1) {
        // base case
        return n;
    }
    // 分别代表 dp[i - 1] 和 dp[i - 2]
    int dp_i_1 = 1, dp_i_2 = 0;
    for (int i = 2; i <= n; i++) {
        // dp[i] = dp[i - 1] + dp[i - 2];
        int dp_i = dp_i_1 + dp_i_2;
        // 滚动更新
        dp_i_2 = dp_i_1;
        dp_i_1 = dp_i;
    }
    return dp_i_1;
}

```



凑零钱：

```java
class Solution {
    static int[] dp ;

    public int coinChange(int[] coins, int amount) {
         dp = new int[amount +1 ];
         Arrays.fill(dp, -777);
        return cc(coins, amount);
    }
    public int cc(int[] coins, int amount) {
       
        if (amount < 0) return -1;
        else if (amount == 0) return 0;
        if(dp[amount] != -777){
            return dp[amount];
        }
        int res = Integer.MAX_VALUE;
        for (int coin : coins) {
            int left = amount - coin;
            
             int tmp = 1+  cc(coins, left);
            
            if (tmp >0) {
                res = Math.min(res, tmp);
            }
        }
        int r = res == Integer.MAX_VALUE ? -1 : res;
        dp[amount] = r;
        return r;
    }
}




class Solution {
    
    public int coinChange(int[] coins, int amount) {
        int []dp = new int[amount + 1];
        Arrays.fill(dp, amount+1);
        dp[0] = 0;
        for(int i = 0; i <=amount; i++){
            for(int coin: coins){
                int c = i -coin;
                if(c<0)continue;
                dp[i] = Math.min(dp[i], 1+ dp[c]) ;
            }
        }
        return  dp[amount] == amount +1? -1 : dp[amount];
    }

}
```

**计算机解决问题其实没有任何奇技淫巧，它唯一的解决办法就是穷举**，穷举所有可能性。算法设计无非就是先思考“如何穷举”，然后再追求“如何聪明地穷举”。

列出状态转移方程，就是在解决“如何穷举”的问题。之所以说它难，一是因为很多穷举需要递归实现，二是因为有的问题本身的解空间复杂，不那么容易穷举完整。

备忘录、DP table 就是在追求“如何聪明地穷举”。用空间换时间的思路，是降低时间复杂度的不二法门，除此之外，试问，还能玩出啥花活？

#### 回溯算法解题套路框架

回溯算法和我们常说的 DFS 算法非常类似，本质上就是一种暴力穷举算法。回溯算法和 DFS 算法的细微差别是：**回溯算法是在遍历「树枝」，DFS 算法是在遍历「节点」**

解决一个回溯问题，实际上就是一个决策树的遍历过程，站在回溯树的一个节点上，你只需要思考 3 个问题：

1、路径：也就是已经做出的选择。

2、选择列表：也就是你当前可以做的选择。

3、结束条件：也就是到达决策树底层，无法再做选择的条件。

```java
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

**其核心就是 for 循环里面的递归，在递归调用之前「做选择」，在递归调用之后「撤销选择」**

全排列：

我的解法：

```java

public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> res = new LinkedList<>();
    per(nums, 0, new LinkedList<>(), res);
    return res;
}
// start:开始位置
private void per(int[] nums, int start, LinkedList<Integer> p,  List<List<Integer>> res){
    int len = nums.length;
    // 如果到达末尾，加入结果
    if(start==len){
        List<Integer> list = new ArrayList<>(p);
        res.add(list);
    }else{
        // 否则，从start到末尾，逐个选择，然后把选择项和start处元素交换，递归指定start+1处
        for(int i = start; i< len ;i++){
            p.addLast(nums[i]);
            swap(nums, start, i);
            per(nums, start+1, p, res);
            // 递归结束，复原交换，并且移除尾部元素
            swap(nums, start, i);
            p.removeLast();
        }
    }
}
private void swap(int []nums, int i, int j){
    int tmp = nums[i];
    nums[i] = nums[j];
    nums[j] = tmp;
}
```

```
for 选择 in 选择列表:
    # 做选择 (做选择最简单是用一个使用数组)
    将该选择从选择列表移除
    路径.add(选择)
    backtrack(路径, 选择列表)
    # 撤销选择
    路径.remove(选择)
    将该选择再加入选择列表
```

**回溯算法的一个特点，不像动态规划存在重叠子问题可以优化，回溯算法就是纯暴力穷举，复杂度一般都很高**。

全排列模板！！！：

```java
    // used数组，记录使用过的节点，以便正确地回溯
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new LinkedList<>();
        boolean [] used = new boolean[nums.length];
        p(nums, used, new LinkedList<>(), res);
        return res;
    }

    private void p(int[] nums, boolean[]used, LinkedList<Integer> p,  List<List<Integer>> res){
        if(p.size() == nums.length){
            res.add(new ArrayList<>(p));
            return;
        }
        for(int i = 0; i < nums.length; i++){
            if(used[i]){
                continue;
            }
            used[i] = true;
            p.addLast(nums[i]);
            p(nums, used, p, res);
            p.removeLast();
            used[i] = false;
        }
    }
```



N皇后：

```java

    public List<List<String>> solveNQueens(int n) {
        List<List<String>> res = new LinkedList<>();
        boolean[] used = new boolean[n];
        nq(n, used, res, new LinkedList<>());
        return res;
    }

    private String getDots(int n) {
        StringBuilder str = new StringBuilder();
        for (int i = 0; i < n; i++) {
            str.append(".");
        }
        return str.toString();
    }

    private void nq(int n, boolean[] used, List<List<String>> res, LinkedList<Integer> list) {
        if (!isValid(list)) {
            // 不合法，直接返回
        } else if (list.size() == n) {
            // 合法，且到达末尾，加入list
            ArrayList<String> sub = new ArrayList<>();
            for (Integer lineN : list) {
                sub.add(getDots(lineN) + "Q" + getDots(n - lineN - 1));
            }
            res.add(sub);
        } else {
            // 目前合法，继续递归
            for (int i = 0; i < n; i++) {
                if (used[i]) {
                    continue;
                }
                used[i] = true;
                list.addLast(i);
                nq(n, used, res, list);
                list.removeLast();
                used[i] = false;
            }
        }
    }

    /**
     * 判断皇后之间位置是否合法
     *
     * @param list 列表
     * @return boolean
     */
    private boolean isValid(List<Integer> list) {
        int n = list.size();
        for (int i = 1; i < n; i++) {
            // 判断第i行是否和前面的每一行冲突
            for (int j = 0; j < i; j++) {
                if (list.get(j) + 0 == list.get(i)) {
                    return false;
                }
                if (list.get(i) - (i - j) == list.get(j)) {
                    return false;
                }
                if (list.get(i) + (i - j) == list.get(j)) {
                    return false;
                }
            }
        }
        return true;
    }
```

#### 回溯算法秒杀所有排列-组合-子集问题

无论是**排列、组合还是子集**问题，简单说**无非就是让你从序列 `nums` 中以给定规则取若干元素**，主要有以下几种变体：

- **形式一、元素无重不可复选，即 `nums` 中的元素都是唯一的，每个元素最多只能被使用一次，这也是最基本的形式**。 以组合为例，如果输入 `nums = [2,3,6,7]`，和为 7 的组合应该只有 `[7]`。
- **形式二、元素有重不可复选，即 `nums` 中的元素可以存在重复，每个元素最多只能被使用一次**。 以组合为例，如果输入 `nums = [2,5,2,1,2]`，和为 7 的组合应该有两种 `[2,2,2,1]` 和 `[5,2]`。
- **形式三、元素无重可复选，即 `nums` 中的元素都是唯一的，每个元素可以被使用若干次**。以组合为例，如果输入 `nums = [2,3,6,7]`，和为 7 的组合应该有两种 `[2,2,3]` 和 `[7]`

当然，也可以说有第四种形式，即元素可重可复选。但既然元素可复选，那又何必存在重复元素呢？元素去重之后就等同于形式三

上面用组合问题举的例子，但排列、组合、子集问题都可以有这三种基本形式，所以共有 9 种变化

除此之外，题目也可以再添加各种限制条件，比如让你求和为 `target` 且元素个数为 `k` 的组合，那这么一来又可以衍生出一堆变体，怪不得面试笔试中经常考到排列组合这种基本题型。

**但无论形式怎么变化，其本质就是穷举所有解，而这些解呈现树形结构，所以合理使用回溯算法框架，稍改代码框架即可把这些问题一网打尽**。

![img](_images/AlgorithmNotes.asserts/1.jpeg)

![img](_images/AlgorithmNotes.asserts/2.jpeg)

**组合问题和子集问题其实是等价的，这个后面会讲；至于之前说的三种变化形式，无非是在这两棵树上剪掉或者增加一些树枝罢了**。



##### 子集（元素无重不可复选）

子集：https://leetcode.cn/problems/subsets/

```java
    public List<List<Integer>> subsets(int[] nums) {
        trackback(nums, 0);
        return res;
    }
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> track = new LinkedList<>();

    void trackback(int[] nums, int start){
        res.add(new ArrayList<>(track));
        for(int i = start; i<nums.length; i++){
            track.addLast(nums[i]);
            trackback(nums, i+1);
            track.removeLast();
        }
    }
```

##### 组合（元素无重不可复选）

https://leetcode.cn/problems/combinations/submissions/

**组合和子集是一样的：大小为 `k` 的组合就是大小为 `k` 的子集**。

```java
    public List<List<Integer>> combine(int n, int k) {
        int []nums = new int[n];
        for(int i = 1;i <=n ;i++) nums[i-1] = i;
        trackback(nums, 0, k);
        return res;
    }
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> track = new LinkedList<>();

    void trackback(int [] nums, int start, int k){
        if(track.size() == k){
            res.add(new ArrayList<>(track));
            return;
        }
        for(int i = start; i<nums.length;i++){
            track.addLast(nums[i]);
            trackback(nums, i+1, k);
            track.removeLast();
        }
    }


// 没必要使用数组

// 主函数
public List<List<Integer>> combine(int n, int k) {
    backtrack(1, n, k);
    return res;
}

void backtrack(int start, int n, int k) {
    // base case
    if (k == track.size()) {
        // 遍历到了第 k 层，收集当前节点的值
        res.add(new LinkedList<>(track));
        return;
    }
    
    // 回溯算法标准框架
    for (int i = start; i <= n; i++) {
        // 选择
        track.addLast(i);
        // 通过 start 参数控制树枝的遍历，避免产生重复的子集
        backtrack(i + 1, n, k);
        // 撤销选择
        track.removeLast();
    }
}

```

##### 排列（元素无重不可复选）

全排列： 使用used数组是主流的解决方式，更加简单

(但是也可以使用 上述方式 + 交换数组值 的方式)

##### 子集/组合（元素可重不可复选）

子集2： https://leetcode.cn/problems/subsets-ii/

```java
    // 和 无重复的数组 类似，对于重复的数组， 只需要排序，然后在选择是无重地选择
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        // 先排序
        Arrays.sort(nums);
        traceback(nums, 0);
        return res;
    }

    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> trace = new LinkedList<>();

    void traceback(int[] nums, int start){
        res.add(new ArrayList<>(trace));
        // 记录前一个值，用于无重复地选择
        int prev = 100;
        for(int i =start; i <nums.length; i++){
            if(nums[i] != prev){
                trace.addLast(nums[i]);
                traceback(nums, i+1);
                trace.removeLast();
                prev = nums[i];
            }
        }
    }
```



##### 排列（元素可重不可复选）

[全排列 II](https://leetcode.cn/problems/permutations-ii/)

```java
class Solution {
    // 和全排列唯一的区别就是：要求全排列不重复(子集不重复), 这里一个最简单的方式就是使用Set去重
    // SET新加了空间复杂度，最好的方式是：在回溯时剪枝
    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        used = new boolean[nums.length];
        traceback(nums);
        return res;
    }

    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> trace = new LinkedList<>();
    Set<String> set = new HashSet<>();
    StringBuilder sb = new StringBuilder();
    boolean [] used ;

    void traceback_set(int [] nums){
        if(trace.size() == nums.length){
            String str = sb.toString();
            if(!set.contains(str)){
                res.add(new ArrayList<>(trace));
                set.add(str);
            }
            
            return;
        }
        for(int i = 0; i <nums.length ; i++){
            if(used[i]) continue;
            used[i] = true;
            trace.addLast(nums[i]);
            String num = String.valueOf(nums[i]);
            sb.append(num);
            traceback_set(nums);
            sb.delete(sb.length()-num.length(), sb.length());
            trace.removeLast();
            used[i] = false;
        }
    }

    void traceback(int [] nums){
        if(trace.size() == nums.length){
            res.add(new ArrayList<>(trace));
            return;
        }
        for(int i = 0; i <nums.length ; i++){
            if(used[i]) continue;
            // 关键剪纸，TODO 相同元素 的 相对位置保持不变，以保证唯一
            if(i > 0 && nums[i] == nums[i-1] && !used[i-1])continue;
            used[i] = true;
            trace.addLast(nums[i]);
            traceback(nums);
            trace.removeLast();
            used[i] = false;
        }
    }

}
```





##### 子集/组合（元素无重可复选）

[组合总和](https://leetcode.cn/problems/combination-sum/)

```java
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        traceback(candidates, target, 0);
        return res;
    }
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> trace = new LinkedList<>();
    int sum = 0;

    void traceback(int[] nums, int target, int start){
        if(sum == target){
            res.add(new ArrayList<>(trace));
        }else if(sum > target){
            return;
        }else{
            for(int i = start; i <nums.length; i++){
                trace.addLast(nums[i]);
                sum+=nums[i];
                // 这里太巧妙了：如何重复使用一个元素，这里start从i开始，即当前元素还可以再次选择。
                traceback(nums, target, i);
                sum-=nums[i];
                trace.removeLast();
            }
        }
    }
```



重复选择一个元素：递归时当前元素再次选择



##### 排列（元素无重可复选）



##### 总结

由于子集问题和组合问题本质上是一样的，无非就是 base case 有一些区别，所以把这两个问题放在一起看。

**形式一、元素无重不可复选，即 `nums` 中的元素都是唯一的，每个元素最多只能被使用一次**，`backtrack` 核心代码如下：

```java
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i + 1);
        // 撤销选择
        track.removeLast();
    }
}

/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 剪枝逻辑
        if (used[i]) {
            continue;
        }
        // 做选择
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // 撤销选择
        track.removeLast();
        used[i] = false;
    }
}

```

**形式二、元素可重不可复选，即 `nums` 中的元素可以存在重复，每个元素最多只能被使用一次**，其关键在于排序和剪枝，`backtrack` 核心代码如下：

```java
Arrays.sort(nums);
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 剪枝逻辑，跳过值相同的相邻树枝
        if (i > start && nums[i] == nums[i - 1]) {
            continue;
        }
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i + 1);
        // 撤销选择
        track.removeLast();
    }
}


Arrays.sort(nums);
/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 剪枝逻辑
        if (used[i]) {
            continue;
        }
        // 剪枝逻辑，固定相同的元素在排列中的相对位置
        if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
            continue;
        }
        // 做选择
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // 撤销选择
        track.removeLast();
        used[i] = false;
    }
}

```

**形式三、元素无重可复选，即 `nums` 中的元素都是唯一的，每个元素可以被使用若干次**，只要删掉去重逻辑即可，`backtrack` 核心代码如下：

```java
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i);
        // 撤销选择
        track.removeLast();
    }
}


/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        backtrack(nums);
        // 撤销选择
        track.removeLast();
    }
}
```



#### BFS 算法解题套路框架

BFS 的核心思想应该不难理解的，就是把一些问题抽象成图，从一个点开始，向四周开始扩散。一般来说，我们写 BFS 算法都是用「队列」这种数据结构，每次将一个节点周围的所有节点加入队列。

BFS 相对 DFS 的最主要的区别是：**BFS 找到的路径一定是最短的，但代价就是空间复杂度可能比 DFS 大很多**

##### 算法框架

要说框架的话，我们先举例一下 BFS 出现的常见场景好吧，**问题的本质就是让你在一幅「图」中找到从起点 `start` 到终点 `target` 的最近距离，这个例子听起来很枯燥，但是 BFS 算法问题其实都是在干这个事儿**

这个广义的描述可以有各种变体，比如走迷宫，有的格子是围墙不能走，从起点到终点的最短距离是多少？如果这个迷宫带「传送门」可以瞬间传送呢？

再比如说两个单词，要求你通过某些替换，把其中一个变成另一个，每次只能替换一个字符，最少要替换几次？

框架：

```java
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路
    
    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数

    while (q not empty) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将 cur 的相邻节点加入队列 */
            for (Node x : cur.adj()) {
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
            }
        }
        /* 划重点：更新步数在这里 */
        step++;
    }
}
```

队列 `q` 就不说了，BFS 的核心数据结构；`cur.adj()` 泛指 `cur` 相邻的节点，比如说二维数组中，`cur` 上下左右四面的位置就是相邻节点；`visited` 的主要作用是防止走回头路，大部分时候都是必须的，但是像一般的二叉树结构，没有子节点到父节点的指针，不会走回头路就不需要 `visited`



[打开转盘锁](https://leetcode.cn/problems/open-the-lock/)

```java
class Solution {
    // BFS 4个锁的一个状态是一个节点，有8条路径通往其他节点
    // 如 0000 => 1000

    String turnLeft(String lock, int turntableIndex){
        char [] chs = lock.toCharArray();
        if(chs[turntableIndex] == '9') chs[turntableIndex] = '0';
        else chs[turntableIndex]+=1;
        return new String(chs);
    }

    String turnRight(String lock, int turntableIndex){
        char [] chs = lock.toCharArray();
        if(chs[turntableIndex] == '0') chs[turntableIndex] = '9';
        else chs[turntableIndex]-=1;
        return new String(chs);
    }


    public int openLock(String[] deadends, String target) {
        LinkedList<String> q = new LinkedList<>();
        
        Set<String> deadendSet = new HashSet<>();
        for(String deadend: deadends) deadendSet.add(deadend);
        Set<String> visited = new HashSet<>();
        q.add("0000");
        int step = 0;
        
        while(!q.isEmpty()){
            int sz = q.size();
            for(int i = 0; i < sz; i++){
                String lock = q.pollFirst();
                visited.add(lock);
                if(!deadendSet.contains(lock)){
                    if(target.equals(lock))return step;
                    for(int j = 0; j < 4; j++){
                        String leftLock = turnLeft(lock, j);
                        String rightLock = turnRight(lock, j);
                        if(!visited.contains(leftLock)){
                            q.add(leftLock);
                            // visited必须在加入Q的时候加入，不能再取出的时候加入：防止加入两个相同的
                            visited.add(leftLock);    
                        }
                        if(!visited.contains(rightLock)){
                            q.add(rightLock);
                            visited.add(rightLock);    
                        }
                    }
                    
                }
                
            }
            // 注意： step是加在这里，而不是for循环中，for中表示该步中每种选择，而步数实际为1
            step++;
        }
        return -1;
    }
}
```





**为什么 BFS 可以找到最短距离，DFS 不行吗**？

首先，你看 BFS 的逻辑，`depth` 每增加一次，队列中的所有节点都向前迈一步，这保证了第一次到达终点的时候，走的步数是最少的。

BFS 可以找到最短距离，但是空间复杂度高，而 DFS 的空间复杂度较低

，一般来说在找最短路径的时候使用 BFS，其他时候还是 DFS 使用得多一些

##### 双向 BFS 优化

**传统的 BFS 框架就是从起点开始向四周扩散，遇到终点时停止；而双向 BFS 则是从起点和终点同时开始扩散，当两边有交集的时候停止**。



#### 二分搜索 框架

> 有一天阿东到图书馆借了 N 本书，出图书馆的时候，警报响了，于是保安把阿东拦下，要检查一下哪本书没有登记出借。阿东正准备把每一本书在报警器下过一下，以找出引发警报的书，但是保安露出不屑的眼神：你连二分查找都不会吗？于是保安把书分成两堆，让第一堆过一下报警器，报警器响；于是再把这堆书分成两堆…… 最终，检测了 logN 次之后，保安成功的找到了那本引起警报的书，露出了得意和嘲讽的笑容。于是阿东背着剩下的书走了。
>
> 从此，图书馆丢了 N - 1 本书

二分查找并不简单，Knuth 大佬（发明 KMP 算法的那位）都说二分查找：**思路很简单，细节是魔鬼**。很多人喜欢拿整型溢出的 bug 说事儿，但是二分查找真正的坑根本就不是那个细节问题，而是在于到底要给 `mid` 加一还是减一，while 里到底用 `<=` 还是 `<`。

![img](_images/AlgorithmNotes.asserts/poem.png)



二分框架：

```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;

    while(...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```

**另外提前说明一下，计算 `mid` 时需要防止溢出**，代码中 `left + (right - left) / 2` 就和 `(left + right) / 2` 的结果相同，但是有效防止了 `left` 和 `right` 太大，直接相加导致溢出的情况。











































