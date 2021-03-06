# 数据结构学习(九)


<!--more-->

# 数据结构——用C语言描述(九)

## 技术

### 排序

#### 内部排序

##### 排序的基本概念

###### 排序

记录序列：{$R_1,R_2,...,R_n$}

关键字序列：{$K_1,K_2,...,K_n$}

重排记录序列，得{$Rp_1,Rp_2,...,Rp_n$}，使相应关键字满足非递减（或非递增）关系，

即$Kp_1≤Kp_2≤...≤Kp_n$

###### 内部排序与外部排序

根据排序时数据所占用**存储器**的不同，可将排序分为两类：

- 内部排序：整个排序过程完全在内存中进行
- 外部排序：由于待排序数据量太大，内存无法容纳全部数据，排序需借外存才能完成

###### 排序的稳定性

- 稳定排序：在排序过程中，相同关键字（$K_i=K_j$,i<j）其领先关系不发生变化——需从算法本身步骤中证明
- 不稳定排序：在排序过程中，相同关键字（$K_i=K_j$,i<j）其领先关系发生变化——仅需举反例即可证明不稳定

###### 基本操作

1. **比较**两个关键字的大小
2. 将记录从一个位置**移动**到另一个位置

###### 存储方法

1. **向量结构**：

   将待排序的记录存放在一组地址连续的存储单元中

2. 链表结构

3. 地址排序

##### 插入类排序

###### 插入排序基本思想

已排序记录子集基础上，将下一个待排序记录**有序插入**到已排序记录子集，直到将所有待排序记录全部插入。

###### 直接插入排序

- 算法思想：将第 i 个插入到前 i-1 个有序集合。

- 算法要点：

  1. 监视哨`r[0]`保存待插入的记录
  2. 方向从后往前查找插入位置
  3. 查找与移动用同一循环完成

- 算法实现：

  ```c
  void InsSort(RecordType r[], int length) //对记录数组r做直接插入排序, length为数组的长度
  {
      int i, j;
      for (i = 2; i < length; i++) //将待插入记录存放到监视哨r[0]中
      {
          r[0] = r[i];
          j = i - 1;
          while (r[0].key < r[j].key) //寻找插入位置
          {
              r[j + 1] = r[j];
              j--;
          }
          r[j + 1] = r[0]; //将待插入记录插入已排序的序列中}
      }
  }
  ```

- 算法分析：

  - 最好情况：

    有序的待排序序列——比较次数 n-1 次，移动次数 2(n-1) 次

  - 最坏情况：

    待排序记录为逆序排列——总比较次数：
    $$
    \sum_{i=2}^ni=\frac{(n+2)(n-1)}{2}
    $$
    记录移动次数：
    $$
    \sum_{i=2}^n(i+1)=\frac{(n+4)(n-1)}{2}
    $$
    故直接插入排序的时间复杂度为$T(n)=O(n^2)$

  - 直接插入排序是稳定排序：

    从后向前进行算法`while(r[0].key<r[j] key)`相同元素插入到`r[j+1]`

###### 折半排序

- 算法改进要点：改进确定插入位置方法

- 算法描述：

  ```c
  void BinSort(RecordType r[], int length) //对数组r进行折半插入排序，length为数组长度
  {
      int i, j, low, high, mid;
      RecordType x;
      for (i = 2; i < length; i++)
      {
          x = r[i];
          low = 1;
          high = i - 1;
          while (low <= high) //确定插入位置
          {
              mid = (low + high) / 2;
              if (x.key < r[mid].key)
                  high = mid - 1;
              else
                  low = mid + 1;
          }
          for (j = i - 1; j >= low; --j)
              r[j + 1] = r[j]; //记录依次向后移动
          r[low] = x;          //插入记录
      }
  }
  ```

- 算法分析：

  折半插入可减少关键字的比较次数，但移动次数没有减少，故折半插入排序的总的时间复杂度仍然是$O(n^2)$。

  每插入一个元素，最大的比较次数为折半判定树的深度。

  插入第 i 个元素，设$i=2^j$，则需进行$\log_2i$次比较，插入 n-1 个元素的平均关键字的比较次数为$O(n\log_2n)$

  折半排序是稳定排序：待插入元素 x 位于有序序列之后，经过条件比较，元素相等时，其插入位置`low=mid+1`右部子表

###### 希尔排序（缩小增量排序）

- 算法改进要点：

  利用直接插入排序的最佳性质：n 比较小、基本有序。

  1. 将记录序列分成若干个子序列分别进行直接插入排序
  2. 经多次调整序列记录已基本有序最后再对记录进行直接插入排序

- 算法思想：

  1. 对整个文件，按间隔$d_1$分组，组内排序
  2. 取$d_2<d_1$（缩小增量），继续以$d_2$为距离排序，直到$d_t=1$（同直接插入排序）为止

  ![image-20220219115543161](https://s2.loli.net/2022/02/19/zGKfOStQIvcVLrj.png)

- 算法描述：

  ```c
  void ShellInsert(RecordType r[], int length, int delta)
  //对记录数组r做一趟希尔插入排序，length为数组长度，delta为增量
  {
      int i, j;
      for (i = 1 + delta; i <= length; i++) // 1+delta为第一个子序列的第二个元素的下标
          if (r[i].key < r[i - delta].key)
          {
              r[0] = r[i]; //备份r[i]（不做监视哨）
              for (j = i - delta; j > 0 && r[0].key < r[j].key; j -= delta)
                  r[j + delta] = r[j];
              r[j + delta] = r[0];
          }
  }
  void ShellSort(RecordType r[], int length, int delta[], int n)
  //对记录数组r做希尔排序，length为数组r长度，delta为增量数组，n为delta[]的长度
  {
      for (int i = 0; i <= n - 1; i++)
          ShellInsert(r, length, delta[i]);
  }
  ```

- 算法分析：

  {{< admonition info >}}

  逆转数是指在此关键字之前比它大的数据个数

  | 待排序序列  | 46   | 55   | 13   | 42   | 94   | 17   | 05   | 90   |
  | ----------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
  | 逆转数$B_i$ | 0    | 0    | 2    | 2    | 0    | 4    | 6    | 1    |

  {{<  /admonition >}}

  - 希尔排序比直接插入排序更快：

    直接插入排序：一次比较移动只减少一个逆转数

    希尔排序：一次比较移动减少逆转数有可能不止一个

  - 根据经验公式可给出缩小增量的取法：

    Shell 提出$d=\frac{n}{2}$，$d=\frac{d}{2}$，直到 d=1为止

    奇数位置元素在最后一趟才会与偶数位置比较，效率较低

    knuth 提出$d=\frac{d}{3}+1$

  - 希尔排序时间复杂度为$O(n^{\frac{3}{2}})$

  - 希尔排序是不稳定排序：

    例如待排序列 {2,4,1,2}，进行希尔排序后变为 {1,2,2,4}，其中相同关键字 2 的领先关系发生了变化。

##### 交换类排序

###### 交换排序基本思想

通过一系列交换逆序元素进行排序

###### 冒泡排序（相邻比序法）

- 算法思想

  顺次比较相邻两元素大小，若逆序就交换位置，反复扫描，直到待排序记录没有逆序为止。

- 算法描述

  ```c
  void BubbSort(RecordType r[], int length) //冒泡排序
  {
      int i, j, n, change;
      RecordType x;
      n = length;
      change = true;
      for (i = 1; i <= n - 1 && change; i++)
      {
          change = false;
          for (j = 1; j <= n - i; j++)
              if (r[j].key > r[j + 1].key)
              {
                  x = r[j];
                  r[j] = r[j + 1];
                  r[j + 1] = x;
                  change = true;
              }
      }
  }
  ```

- 算法分析：

  最坏情况——关键字逆序排列，第 i 趟需 n-i 次比较，3(n-i) 次移动经过 n-1 趟冒泡排序：

  - 比较次数$\frac{n(n-1)}{2}$
  - 移动次数$\frac{3n(n-1)}{2}$
  - 时间复杂度$O(n^2)$
  - 空间复杂度$O(1)$

###### 快速排序

- 改进要点：

  通过两个不相邻元素的比较，一次交换可能消除多个逆序，大大加快排序速度。

- 算法思想：

  选序列中一个枢轴元（选第 1 个其关键字$K_1$）以枢轴元素为界，分为两个子表小于$K_1$在前部子表，大于$K_1$在后部子表，对两个子表继续如上过程，直到所有子表的表长不超过 1 为止。

- 算法描述：

  ```c
  int QKPass(RecordType r[], int left, int right) //一趟排序
  {
      int low, high;
      RecordType x;
      x = r[left];
      low = left;
      high = right;
      while (low < high)
      {
          while (low < high && r[high].key >= x.key)
              high--; //从右到左找小于x.key的记录
          if (low < high)
          {
              r[low] = r[high];
              low++; //置入左r[low]
          }
          while (low < high && r[low].key <= x.key)
              low++; //从左到右找大于x.key的记录
          if (low < high)
          {
              r[high] = r[low];
              high--; //置入右r[high]
          }
      }
      r[low] = x;
      return low;
  }
  void QKSort(RecordType r[], int low, int high) //快速排序
  {
      int pos;
      if (low < high)
      {
          pos = QKPass(r, low, high); //枢轴位置
          QKSort(r, low, pos - 1);    //前部子表
          QKSort(r, pos + 1, high);   //后部子表
      }
  }
  ```

- 算法分析：

  - 最好情况：

    每趟将序列一分两半，正好在表中间，将表分成两个大小相等的子表，类似折半查找

    时间复杂度$O(n\log_2n)$

  - 最坏情况：

    已经排好序共需进行 n- 1 趟排序,其比较次数为：
    $$
    \sum_{i=1}^{n-1}(n-i)=\frac{n(n-1)}{2}
    $$
    时间复杂度$O(n^2)$

  - 平均时间复杂度：$T_{avg}(n)=n\log_2n$

##### 选择类排序

###### 选择排序基本思想

每一趟在 n-i+1 (i=1,2,…n-1) 个记录中选取关键字最小的记录作为有序序列中第 i 个记录

###### 简单选择排序

- 算法思想：第 i 趟排序从 n-i+1 个记录中选出关键字最小的记录，并和第 i 个元素进行交换

- 算法描述：

  ```c
  //对记录数组r做简单选择排序，n为数组长度
  void SelectSort(RecordType r[], int n)
  {
      int i, j, k;
      RecordType x;
      for (i = 1; i <= n - 1; i++)
      {
          k = i;
          for (j = i + 1; j <= n; j++)
              if (r[j].key < r[k].key)
                  k = j;
          if (k != i)
          {
              x = r[i];
              r[i] = r[k];
              r[k] = x;
          }
      }
  }
  ```

- 算法分析：

  - 最好情况：

    待排序记录初始状态就已经是正序排列了，则不需要移动记录

  - 最坏情况：

    待排序记录初始状态是按逆序排列的，则需要移动记录的次数最多为 3(n-1) 

  - 时间复杂度：

    由于每次都没有利用上次比较的结果，故时间复杂度为$O(n^2)$

  - 简单选择排序是不稳定排序：

    例如，待排序列 {3,3,2} 经过简单选择排序变为 {2,3,3}，相同关键字 3 的领先关系发生了变化

###### 树形选择排序

- 算法改进要点：

  把比较过程中的大小关系保存下来以降低比较次数

- 基本思想：

  1. 把待排序的 n 个记录的关键字两两进行比较，取出较小者

  2. 在$[\frac{n}{2}]$个较小者中，采用同样的方法进行比较选出每两个中的较小者。如此反复，直至选出最小关键字记录为止

- 算法分析：

  每趟选出较小关键字需$\log_2n$次比较，n 趟比较时间复杂度$O(n\log_2n)$

  移动记录次数不超过比较次数。

  总的时间复杂度为$O(n\log_2n)$。

  与简单选择排序相比较，其降低了比较次数的数量级，增加了 n-1个非叶子节点的辅助存储空间也就是以空间换时间。

###### 堆排序

- 算法改进要点

  将待排序数组看成一棵完全二叉树，结点`r[i]`的左孩子是`r[2i]`，右孩子`r[2i+1]`，双亲是`r[i/2]`。

  通过调整完全二叉树建堆及重建堆选择出关键字最小记录及次小记录等实现排序。

- 算法思想

  - 堆

    将待排序数组 r 看成一棵完全二叉树的顺序表示，对二叉树进行调整，满足下列条件为堆：

    $r[i].key≥r[2i].key$并且$r[i].key≥r[2i+1].key$ (i=1,2,…)

    或者$r[i].key≤r[2i].key$并且$r[i].key≤r[2i+1].key$ (i=1,2,…)

  - 大根堆：完全二叉树中任意节点的关键字大于或等于其左孩子和右孩子的关键字

  - 小根堆：完全二叉树中任意节点的关键字小于或等于其左孩子和右孩子的关键字

- 堆排序问题

  以大根堆为例

  1. 按堆定义建初堆

  2. 去掉最大元之后重建堆，得到次大元
  3. 如此类推

  - 重建堆：当堆顶记录改变时，如何重建堆？

    1. 将完全二叉树根结点中关键字 x 移出，此时根结点相当于空结点

    2. 从左、右孩子中选出关键字最大的记录,如果该记录的关键字大于 x，则将该记录上移至空结点
    3. 重复上述过程，直到空结点左、右孩子关键字均不大于 x，此时，将待调整记录 x 放入空结点即可

    ```c
    //重建堆，r[k..m]是以r[k]为根的完全二叉树，其左右子树为大根堆
    void sift(RecordType r[], int k, int m)
    {
        RecordType t;
        KeyType x;
        int i, j;
        bool finished;
        t = r[k]; //暂存根记录r[k]
        x = r[k].key;
        i = k;
        j = 2 * i;
        finished = false;
        while (j <= m && !finished)
        {
            if (j < m && r[j].key < r[j + 1].key) //若存在右子树且根的关键字大则沿右分支筛选
                j++;
            if (x >= r[j].key) //筛选完备
                finished = true;
            else //继续筛选
            {
                r[i] = r[j];
                i = j;
                j = 2 * i;
            }
        }
        r[i] = t; // r[k]填入到恰当位置
    }
    ```

  - 建初堆：如何由一个任意序列建初堆？

    筛选需从最后一个子树位置$\lfloor\frac{n}{2}\rfloor$开始，可以反复利用重建堆法自底向上，把所有的子树逐层调整为堆，直至根结点。

    ```c
    //对记录数组r重建堆，length为数组长度
    void crt_heap(RecordType r[], int length)
    {
        int i, n;
        n = length;
        for (i = n / 2; i >= 1; i--) //自第i个记录开始筛选建堆
            sift(r, i, n);
    }
    ```

  - 堆排序算法：

    1. 建初堆：从最后一子树$\frac{n}{2}$直到根建堆
    2. 将（堆顶）第一个元素与最后一个元素互换;去掉最后元素，将剩余元素调整建堆，再转出堆顶元素
    3. 重复执行步骤 2、3 共 n-1 次，直到序列有序

    ```c
    //对r[1..n]堆排序，关键字由大到小排序
    void HeapSort(RecordType r[], int length)
    {
        int i, n;
        RecordType b;
        crt_heap(r, length); //建初堆
        n = length;
        for (i = n; i >= 2; i--) //将堆顶记录和堆的最后一个记录互换
        {
            b = r[1];
            r[i] = b;
            sift(r, 1, i - 1); //进行调整，使r[1..i-1]变成堆
        }
    }
    ```

- 算法分析：

  1. 重建堆的时间复杂度为$O(n\log_2n)$需 n-1 次重建堆

     建初堆时间复杂度为$O(n\log_2n)$，堆排序时间复杂度为$O(n\log_2n)$

  2. 堆排序与树型排序相比较，排序中只需要存放一个记录的辅助空间，因此也将堆排序称作原地排序

  3. 堆排序是一种**不稳定**的排序方法，它不适用于待排序记录个数 n 较少的情况，但对 n 较大的文件还是很有效的

     ![image-20220219224827278](https://s2.loli.net/2022/02/19/7A46HGEzmcXj5t3.png)

| 选择类排序比较 | 时间复杂度    | 空间复杂度 |
| -------------- | ------------- | ---------- |
| 简单选择       | $O(n^2)$      | $O(1)$     |
| 树型选择       | $O(n\log_2n)$ | $O(n)$     |
| 堆排序         | $O(n\log_2n)$ | $O(1)$     |

##### 归并类排序

###### 基本思想

基于合并的思想，将两个或两个以上有序表合并成一个新的有序表。

2-路归并排序：

1. 初始：n 个记录看成 n 个长度为 1 的有序子序列
2. 两两归并得$\lfloor\frac{n}{2}\rfloor$个长度为 2 的有序子序列
3. 两两归并得到若干个长度为 4 的有序子序列 i
4. 如此重复，直到得到长度为n的有序序列为止

###### 算法描述

```c
//初始调用，对数组r[1..n]做归并排序
void MergeSort(RecordType r[], int n)
{
    MSort(r, 1, n, r);
}
//一趟合并算法
void Merge(RecordType r1[], int low, int mid, int high, RecordType r[])
{
    int i, j, k;
    i = low;
    j = mid + 1;
    k = low;
    while ((i <= mid) && (j <= high)) //对序列的前后两半部分进行比较
    {
        if (r1[i].key <= r1[j].key)
        {
            r[k] = r1[i];
            i++;
        }
        else
        {
            r[k] = r1[j];
            j++;
        }
        k++;
    }
    while (i <= mid) //合并两个子序列
    {
        r[k] = r1[i];
        k++, i++;
    }
    while (j <= high)
    {
        r[k] = r1[j];
        k++, j++;
    }
}
// r1[low..high]归并排序后放入r3[low..high]
void MSort(RecordType r1[], int low, int high, RecordType r3[])
{
    int mid;
    RecordType r2[N]; //辅助空间
    if (low == high)
        r3[low] = r1[low];
    else
    {
        mid = (low + high) / 2;
        MSort(r1, low, mid, r2);       //前半部分归并
        MSort(r1, mid + 1, high, r2);  //后半部分归并
        Merge(r2, low, mid, high, r3); // r2合并放入r3
    }
}
```

###### 算法分析

- 一趟归并排序的时间复杂度为$O(n)$，将`r1[1..n]`前后两个有序子表归并后并存放到`r2[1..n]`中

- 归并排序的时间复杂度为$O(n\log_2n)$，整个归并排序进行$\log_2n$趟2-路归并，每趟归并是$O(n)$

- 归并排序的空间复杂度为$O(n)$，归并排序需用到与待排序序列等量的辅助空间`r2[n]`

  这也使归并排序适合于外部排序：

  - 归并路数取决于外部设备的台数
  - 归并长度取决于内存缓冲区的容量
  - 支持外存分批并行调入内存，提高效率

- 归并排序是稳定排序：

  在一趟归并算法中，前部相同元素先于后部置入归并排序后的表，使其相同关键字的领先关系不发生变化。

##### 分配类排序

利用基于分配的思想通过分配和叫收集两种操作实现排序。

###### 多关键字排序

- 高位优先排序法
- 低位优先排序法

{{< admonition >}}

字典就是采用的多关键字排序

{{< /admonition >}}

###### 链式基数排序

- 存储结构：用链表存储待排序表

- 排序思想：多趟分配和收集完成排序（趟数为关键字位数）

- 基数 RD：关键字符号的个数（十进制的基数为 0~9）

- 算法思想：

  按最低位的值对记录进行初步排序，在此基础上按次低位的值进行进一步排序，按由低向高，直到最高位，关键字的每一位都排序。

- 算法描述：

  ```c
  #define RADIX 10
  #define KEY_SIZE 6
  #define LIST_SIZE 20
  typedef struct
  {
      KeyType key[KEY_SIZE]; //子关键字数组
      OtherType other_data;  //其他数据项
      int next;              //静态链域
  } RecordType1;
  typedef struct
  {
      RecordType1 r[LIST_SIZE + 1]; // r[0]为头结点
      int length;
      int keynum;
  } SLinkList; //静态链表
  typedef int PVector[RADIX];
  PVector head, tail;
  void RadixSort(RecordType1 r[], int length, int keynum)
  {
      int d, i, n;
      n = length;
      for (i = 0; i < n; i++) //构造动态链表
          r[i].next = i + 1;
      r[n].next = 0;
      d = keynum;
      for (i = d - 1; i >= 0; i--) //从最低位子关键字开始，进行d趟分配收集
      {
          Distribute(r, i, head, tail); //第i趟分配
          Collect(r, head, tail);       //第i趟收集
      }
  }
  void Distribute(RecordType1 r[], int i, PVector head, PVector tail)
  {
      int j, p;
      for (j = 0; j <= RADIX - 1; j++)
          head[j] = 0; // RADIX个队列初始化为空
      p = r[0].next;
      while (p)
      {
          j = Order(r[p].key[i]); //用第i位关键字求相应队列号
          if (!head[j])
              head[j] = p; //将p结点插入第j个队列
          else
              r[tail[j]].next = p;
          tail[j] = p;
          p = r[p].next;
      }
  }
  void Collect(RecordType1 r[], PVector head, PVector tail)
  {
      int j, t;
      while (!head[j]) //找第一个非空队列
          j++;
      r[0].next = head[j];
      t = tail[j];
      while (j < RADIX - 1) //寻找并串接所有非空队列
      {
          j++;
          while ((j < RADIX - 1) && (!head[j]))
              j++;     //找下个非空队列
          if (head[j]) //链接非空队列
          {
              r[t].next = head[j];
              t = tail[j];
          }
      }
      r[t].next = 0;
  }
  ```

- 算法分析：

  - n 个记录（每记录含 d 位,每位取值 RD 个值）
  - 一趟分配算法的时间复杂度为$O(n)$
  - 一趟收集算法的时间复杂度为$O(RD)$
  - 共进行 d 趟分配与收集，总时间复杂度为$O(d(n+RD))$
  - 辅助空间为$O(RD)$

###### 基数排序的顺序表实现

按照稀疏矩阵转置中的统计方法实现

例：待排序序列218，109，063，930，589，184，505，269，008，083

关键字$k_1、k_2、k_3$，$k_3$位扫描，统计$k_3$位为 0 到 9 的记录个数`num[10]`

结果如下：

|        | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
| ------ | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| num[]  | 1    | 0    | 0    | 2    | 1    | 1    | 0    | 0    | 2    | 3    |
| cpos[] | 0    | 1    | 1    | 1    | 3    | 4    | 5    | 5    | 5    | 7    |

$k_3$位上值为 i 的记录在排好序向量中第一次出现位置`cpos[i]`，由`cpos[0]=0`可确定`cpos[i]=cpos[i-1]+num[i-1]`，完成按$k_3$位进行的排序。

$k_2$和$k_1$位以此类推，分别统计、定位与置放，完成基数排序。

##### 比较

###### 性能比较

|          | 平均时间复杂度 | 最坏时间复杂度 | 辅助存储空间 |
| -------- | -------------- | -------------- | ------------ |
| 简单排序 | $O(n^2)$       | $O(n^2)$       | $O(1)$       |
| 快速排序 | $O(n\log_2n)$  | $O(n^2)$       | $O(1)$       |
| 堆排序   | $O(n\log_2n)$  | $O(n\log_2n)$  | $O(1)$       |
| 归并排序 | $O(n\log_2n)$  | $O(n\log_2n)$  | $O(n)$       |
| 基数排序 | $O(d(n+RD))$   | $O(d(n+RD))$   | $O(rd)$      |

###### 稳定性比较

|              | 稳定性 | 反例                       |
| ------------ | ------ | -------------------------- |
| 直接插入排序 | 是     |                            |
| 冒泡排序     | 是     |                            |
| 简单选择排序 | 否     | (3,3,2)                    |
| 希尔排序     | 否     | (2,4,1,2)，$d_1$=2,$d_2$=1 |
| 快速排序     | 否     | (3,2,2)                    |
| 堆排序       | 否     | (5,5,3)                    |
| 归并排序     | 是     |                            |
| 基数排序     | 是     |                            |

##### 结论

1. **简单排序**一般只用于**n较小**的情况。当序列中的记录**基本有序**时，直接插入排序是最佳的排序方法，其常与其他排序方法组合使用
2. **快速排序**平均时间性能是最好的
3. 堆排序和归并排序的最坏时间复杂度仍为$O(n\log_2n)$，当**n较大**时，**归并排序**的时间性能优于堆排序，但它所需的**辅助空间最多**
4. **基数排序**最适用于**n很大**而**关键字的位数d较小**的序列。

