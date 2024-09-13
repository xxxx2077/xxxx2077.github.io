# 算法基础

## 二分查找

#### 查找大于等于x 的第一个数

a[mid] >= target说明要寻找的数在[l, mid]中

a[mid] < target说明要寻找的数在(mid, n - 1]中

```C++
// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (a[mid] < target) l = mid + 1;    // check()判断mid是否满足性质
        else r = mid ;
    }
    return l;
}
```

#### 查找小于等于x 的最后一个数

a[mid] >= target说明要寻找的数在[l, mid]中

a[mid] < target说明要寻找的数在(mid, n - 1]中

```C++
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (a[mid] > target) r = mid - 1;
        else l = mid;
    }
    return l;
}
```

#### 记忆口诀

大于等于x：

- r = mid
- 因为是大于，所以l 是 mid + 1

小于等于x：

- l = mid
- 因为是小于，所以r 是mid - 1

## 动态规划

动态规划可根据以下两个维度思考：

1. 状态表示
   - 集合
   - 属性
2. 状态计算
3. 时间复杂度计算：状态数 * 状态转移计算次数

### 背包问题

给出N个物品的体积$v_i$和价值$w_i$，以及背包的总容量V

求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。

输出最大价值。

#### 01背包问题

> 模板题：https://www.acwing.com/problem/content/2/

**特点：**每件物品只能使用一次

**状态计算：**

由于每个物品只能使用一次，那么分类讨论为：

1. 第i个物品不装进书包：`f[i][j]=f[i - 1][j]`
2. 第i个物品装进书包：`f[i][j] =f[i - 1][j - v[i]] + w[i]` 

`f[i][j]取以上结果的最大值max即可，即f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i])`

##### 朴素版

采用二维数组`f[i][j]`作为状态表达式，表示**前i个物品背包容积为j的所有选择**

```c++
#include <iostream>
using namespace std;

const int N = 1010, V = 1010;
//  f[N][V] 状态表达式
//  集合：f[i][j] 表示 前i个物品背包容积为j的所有选择
//  属性：所有选择的价值最大值
int f[N][V];

// v[i] 记录每个物品的体积，w[i] 记录每个物品的价值
int v[N],w[N];

int main(){
    int n, m;
    cin >> n >> m;
    //  输入    
    for(int i = 1; i <= n; i++){
        cin >> v[i] >> w[i];
    }
    
    //  开始求解
    //  f[0][0~m]都是0，因为第0个物品没有价值可言
    //  从第1个物品开始计算
    for(int i = 1; i <= n; i++){
        //  遍历每个容积
        for(int j = 0; j <= m; j++){
            //  如果不选第i个物品
            f[i][j] = f[i - 1][j];
            //  如果选择第i个物品
            //  判断当前容积是否能容纳第i个物品
            if(j >= v[i])   
                // f[i - 1][j - v[i]]表示选择第i个物品的前提下，第i - 1个物品的价值最大值
                f[i][j] = max(f[i][j], f[i - 1][j - v[i]] + w[i]);
        }
    }
    
    //  输出结果，f[n][m]表示前n个物品容积为m的价值最大值，即我们问题的解
    cout << f[n][m] << endl;
}
```

**时间复杂度：**$O(NV)\approx O(n^2)$

**空间复杂度：**$O(NV)\approx O(n^2)$

##### 优化版

优化思路：

1. 计算`f[i]`都是依靠`f[i - 1]`，因此本质上只需要一维数组，这种称为滚动数组（滚动数组：二维数组计算的时候`f[i]`只依托于`f[i - 1]`等有限个前驱数组）
2. 遍历容积都是从`j == 0`开始，但是又有判断`j >= v[i]`才会计算`f[i][j] = max(f[i][j], f[i - 1][j - v[i]] + w[i]);`因此可以直接从`j == v[i]`开始

**值得注意的是：**

由于滚动数组只有一维，因此`f[i - 1][j]`实际上以上一次存储的`f[j]`来表示

如果从左往右计算，那么计算`f[i][j]`时，由于`j - v[i] <= j`，`f[j - v[i]]`在`f[j]`的左侧，即已经被计算过

此时`f[j - v[i]]`表示的是`f[i][j - v[i]]`而不是`f[i - 1][j - v[i]]`，与题意不符

因此我们需要从右往左计算，保证计算顺序`f[j]`早于`f[j - v[i]]`

```c++
#include <iostream>
using namespace std;

const int N = 1010, V = 1010;
//  f[V] 状态表达式
//  集合：遍历第i次，f[j] 表示 前i个物品背包容积为j的所有选择
//  属性：所有选择的价值最大值
int f[V];

// v[i] 记录每个物品的体积，w[i] 记录每个物品的价值
int v[N],w[N];

int main(){
    int n, m;
    cin >> n >> m;
    //  输入    
    for(int i = 1; i <= n; i++){
        cin >> v[i] >> w[i];
    }
    
    //  开始求解
    //  f[0~m]都是0，因为第0个物品没有价值可言
    //  从第1个物品开始计算
    for(int i = 1; i <= n; i++){
        //  遍历每个容积
        for(int j = m; j >= v[i]; j--){
            f[j] = max(f[j], f[j - v[i]] + w[i]);
        }
    }
    
    //  输出结果，已经遍历了n次，f[m]表示前n个物品容积为m的价值最大值，即我们问题的解
    cout << f[m] << endl;
}
```

**时间复杂度：**$O(NV)\approx O(n^2)$

**空间复杂度：**$O(V)\approx O(n)$

#### 完全背包问题

> 模板题：https://www.acwing.com/problem/content/3/

**特性：**每种物品都有无限件可用。

**状态计算：**

以物品的个数作为分类讨论条件：

1. 第i个物品取0个：`f[i - 1][j] = f[i][j - 0*v[i]]+0*w[i]`
2. 第i个物品取1个：`f[i][j - 1*v[i]]+1*w[i]`
3. ....
4. 第i个物品取k个：`f[i][j - k*v[i]]+k*w[i]`
5. ....

保证`k * v[i] <= j`的前提，把以上状态全部取个max得到结果

##### 最初版

```c++
#include <iostream>
using namespace std;

const int N = 1010, V = 1010;
int f[N][V];
int v[N], w[N];

int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
        cin >> v[i] >> w[i];
    for(int i = 1; i <= n; i++){
        for(int j = 0; j <= m; j++){
            for(int k = 0; k * v[i] <= j; k++){
                f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i]);
            }
        }
    }
    
    cout << f[n][m] << endl;
}
```

**时间复杂度：**$O(NV*\frac{V}{v})\approx O(n^3)$

**空间复杂度：**$O(NV)\approx O(n^2)$

该方法时间复杂度过于高，基本不使用



##### 普通优化版

```
f[i][j] 
= max(f[i - 1][j], f[i - 1][j - v[i]] + w[i], f[i - 1][j - 2 * v[i]] + 2 * w[i],f[i - 1][j - 3 * v[i]] + 2 * w[i]， ..., f[i - 1][j - k * v[i]] + k * w[i]     ,...)

f[i][j - v[i]] 
= max(             f[i - 1][j - v[i]]       , f[i - 1][j - 2 *v[i]] + w[i],      f[i - 1][j - 3 * v[i]] + 2 * w[i], ..., f[i - 1][j - (k + 1) * v[i]] + k * w[i],...)

可以发现
`f[i][j] 
= max(f[i - 1][j], f[i][j - v[i]] + w[i])`
```

我们得到新的状态计算方程：

`f[i][j] = max(f[i - 1][j], f[i][j - v[i]] + w[i])`

**注意：**

完全背包问题的状态方程与01背包问题不同

- 01背包问题的状态计算方程为`f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i])`，这里是 i - 1
- 完全背包问题的状态计算方程为`f[i][j] = max(f[i - 1][j], f[i][j - v[i]] + w[i])`， 这里是 i

```C++
#include <iostream>
using namespace std;

const int N = 1010, V = 1010;
int f[N][V];
int v[N], w[N];

int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
        cin >> v[i] >> w[i];
    for(int i = 1; i <= n; i++){
        for(int j = 0; j <= m; j++){
            f[i][j] = f[i - 1][j];
            if(j >= v[i])
                f[i][j] = max(f[i][j], f[i][j - v[i]] + w[i]);
        }
    }
    
    cout << f[n][m] << endl;
}
```

**时间复杂度：**$O(NV)\approx O(n^2)$

**空间复杂度：**$O(NV)\approx O(n^2)$

与01背包问题类似，该方法还能优化为一维数组版



##### 最终优化版

优化思路与01背包问题大致相同

**唯一不同点在于：**

本次是从左往右遍历容积j（01背包是从右往左），这是因为状态计算方程不同：

- 01背包问题的状态计算方程为`f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i])`，这里是 i - 1
- 完全背包问题的状态计算方程为`f[i][j] = max(f[i - 1][j], f[i][j - v[i]] + w[i])`， 这里是 i

`f[i][j - v[i]]`与`f[i][j]`在同一行，优化为一维数组后，为`f[j - v[i]]`，在`f[j]`的左边，计算顺序：`f[j - v[i]]`先于`f[j]`

所以从左往右遍历

```c++
#include <iostream>
using namespace std;

const int N = 1010, V = 1010;
int f[V];
int v[N], w[N];

int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
        cin >> v[i] >> w[i];
    for(int i = 1; i <= n; i++){
        // 从左往右遍历
        for(int j = v[i]; j <= m; j++){
            f[j] = max(f[j], f[j - v[i]] + w[i]);
        }
    }
    
    cout << f[m] << endl;
}
```



#### 多重背包问题

> 模板题：
>
> - 不限时版：https://www.acwing.com/problem/content/4/
> - 限时版：https://www.acwing.com/problem/content/5/

**特性：**每个物品最多选择s[i]件

##### 朴素版

最基础的想法：枚举物品选择的件数k

```c++
#include <iostream>
using namespace std;

const int N = 1010, V = 1010;
int f[N][V];
int v[N], w[N], s[N];

int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++){
        cin >> v[i] >> w[i] >> s[i];
    }
    for(int i = 1; i <= n; i++){
        for(int j = 0; j <= m; j++){
            for(int k = 0; k <= s[i] && k * v[i] <= j; k++){
                f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i]);
            }
        }
    }
    
    cout << f[n][m] << endl;
}
```

**时间复杂度：**$O(NVs)\approx O(n^3)$

**空间复杂度：**$O(NV)\approx O(n^2)$



##### 优化版

与完全背包问题不同，多重背包多了一项，因此不能采用完全背包问题的做法

```
f[i][j] 
= max(f[i - 1][j], f[i - 1][j - v[i]] + w[i], f[i - 1][j - 2 * v[i]] + 2 * w[i],f[i - 1][j - 3 * v[i]] + 2 * w[i]， ..., f[i - 1][j - k * v[i]] + k * w[i]     ,..., f[i - 1][j - s[i] * v[i]] + s[i] * w[i])

f[i][j - v[i]] 
= max(             f[i - 1][j - v[i]]       , f[i - 1][j - 2 * v[i]] + w[i],     f[i - 1][j - 3 * v[i]] + 2 * w[i], ..., f[i - 1][j - (k + 1) * v[i]] + k * w[i],..., f[i - 1][j - s[i] * v[i]] + (s[i] - 1) * w[i], f[i - 1][j - (s[i] + 1) * v[i]] + s[i] * w[i])

与01背包不同，不能再写成：
f[i][j] 
= max(f[i - 1][j], f[i][j - v[i]] + w[i])

因为f[i][j - v[i]]比f[i][j]多了一项f[i - 1][j - (s[i] + 1) * v[i]] + s[i] * w[i]，多出来的一项记作d
所以f[i][j - v[i]] = max(B, d)， B为d前面若干项

f[i][j] = max(f[i - 1][j], f[i][j - v[i]] + w[i])
f[i][j - v[i]] = max(B , d)
-> f[i][j] = max(f[i - 1][j], max(B , d) + w[i])
无法计算B，因此这种方法不可取
```



> 回顾多重背包朴素版解法存在的问题：需要枚举每个物品的件数，而且这一步不能像完全背包问题那样优化，导致时间复杂度过高
>
> 而01背包不需要枚举每个物品的件数，因为物品最多只有1件
>
> 我们能否将多重背包问题转换为01背包问题？

优化版采用二进制分组表示法，将物品的件数k分解为若干个二进制组，那么枚举k变成了枚举每个组是否需要

因此多重背包问题转换为了01背包问题的解法

接下来我们看如何将k转换为二进制组

**优化解法**

理论前提：

- 任何数都能由二进制1，2，4，8，...，表示，例如$11=(1011)_2=8+0+2+1$

根据该前提，我们得出物品的件数s也能由二进制表示，枚举物品件数变成了枚举二进制组的个数

时间复杂度由$O(n^3)$缩减为$O(n^2log(s))$，s为物品的件数，$log(s)$为二进制组的个数



`f[i][j]`状态数$i$由$n$个变成$n*log_2(s)$

log2可这么求

```c++
#include <cmath>
using namespace std;

int main(){
  	int n;
		cin >> n;
  	cout << log2(n) << endl;
}
```

实际代码中，我们直接将求得log2结果与物品数上限N相乘得到新的N，这个N表示总状态数上限

```c++
#include <iostream>
using namespace std;

const int N = 11010, V = 2010;
int v[N], w[N];
int f[V];

int main(){
    int n, m;
    cin >> n >> m;
    // cnt为分组序数
    // cnt一定要放在循环外面，因为cnt是所有分组的序号
    int cnt = 0;
    // 对每个物品进行二进制组分组
    for(int i = 1; i <= n; i++){
        int a, b, s;
        // a为v[i], b为w[i], s为s[i]
        cin >> a >> b >> s;
        // k为分组内数量
        int k = 1;
        // 对s进行分二进制组
        while(k <= s){
            // 第cnt个组含有k个件数
            cnt++;
            // 计算第cnt个组的体积，每件物品体积相同，都为a
            v[cnt] = a * k;
            // 计算第cnt个组的价值，每件物品价值相同，都为b
            w[cnt] = b * k;
            s -= k;
            k *= 2;
        }
        // 剩余的s不能继续分为二进制组
        if(s > 0){
            cnt++;
            v[cnt] = a * s;
            w[cnt] = b * s;
        }
    }
    
    // 状态数由 n 变成了 n * log2(n)
    n = cnt;
    // 01背包问题
    for(int i = 1; i <= n; i++){
        for(int j = m; j >= v[i]; j--){
            f[j] = max(f[j], f[j - v[i]] + w[i]);
        }
    }
    
    cout << f[m] << endl;
}
```



#### 分组背包问题

> 模板题：https://www.acwing.com/problem/content/9/

**特性：**每组物品有若干个，同一组内的物品最多只能选一个。

由选择某个物品变成了两步：

1. 首先选择分组
2. 选择了分组之后要在组内选择一个物品

即进行两次01背包问题，第一次01背包问题将物品当成组，第二次在组内进行01背包问题

##### 朴素版

```c++
#include <iostream>
using namespace std;

const int N = 110, V = 110, S = 110;
int v[N][S], w[N][S], s[N];
int f[N][V];

// i表示第i组，k表示第i组内的第k件物品，j表示背包容积
int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++){
        cin >> s[i];
        for(int k = 1; k <= s[i]; k++)
            cin >> v[i][k] >> w[i][k];
    }
    
    for(int i = 1; i <= n; i ++){
        for(int j = 0; j <= m; j++){
            f[i][j] = f[i - 1][j];
            // 枚举背包内的每个物品
            for(int k = 1; k <= s[i]; k++){
                // 注意这里的v[i][k]和w[i][k]，而不是v[i][j]和w[i][j]
                if(v[i][k] <= j)
                    f[i][j] = max(f[i][j], f[i - 1][j - v[i][k]] + w[i][k]);
            }
        }
    }
    
    cout << f[n][m] << endl;
}
```

##### 优化版

与01背包问题优化思路完全一致

```c++
#include <iostream>
using namespace std;

const int N = 110, V = 110, S = 110;
int v[N][S], w[N][S], s[N];
int f[V];

// i表示第i组，k表示第i组内的第k件物品，j表示背包容积
int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++){
        cin >> s[i];
        for(int k = 1; k <= s[i]; k++)
            cin >> v[i][k] >> w[i][k];
    }
    
    for(int i = 1; i <= n; i ++){
        // 枚举背包内的每个物品
        for(int j = m; j >= 0; j--){
            for(int k = 1; k <= s[i]; k++){
                if(v[i][k] <= j)
                    // 注意这里的v[i][k]和w[i][k]，而不是v[i][j]和w[i][j]
                    f[j] = max(f[j], f[j - v[i][k]] + w[i][k]);
            }
        }
        
    }
    
    cout << f[m] << endl;
}
```



### 线性DP

线性DP即递推方程具有明显的线性关系，有一维线性和二维线性

背包问题也属于线性DP

#### 数字三角形

> https://www.acwing.com/problem/content/900/

`f[i][j]`表示走到第i行第j列的元素的路径之和

##### 朴素版

从上往下遍历

状态方程`f[i][j] += max(f[i - 1][j - 1], f[i - 1][j])`

```C++
#include <iostream>
using namespace std;

// 三角形的整数可以为负数，要取max，因此边界值为负无穷（-INF）
const int N = 510, INF = 0x3f3f3f3f;
// 状态表达式，到达第i行第j列的元素时所有路径的集合，对应的值为路径数字之和最大值
int f[N][N];
int n;

int main(){
    cin >> n;
    // 处理边界条件
    for(int i = 0; i <= n; i++)
        //注意是 i + 1
        for(int j = 0; j <= i + 1; j++)
            f[i][j] = -INF;
    // 输入三角形
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= i; j++)
            cin >> f[i][j];
    // 从第二行开始计算状态方程
    for(int i = 2; i <= n; i++){
        for(int j = 1; j <= i; j++){
            f[i][j] += max(f[i - 1][j - 1], f[i - 1][j]);
        }
    }
    
    // 这里res一定要设置为负无穷，因为数字之和可能为负数
    int res = -INF;
    // 遍历三角形底层取得最大值
    for(int j = 1; j <= n; j++)
        res = max(res, f[n][j]);
    
    cout << res << endl;
}
```

**时间复杂度：**$O(n^2)$

**空间复杂度：**$O(n^2)$

缺点是需要处理边界条件

##### 优化版

从下往上遍历

优化点：由于最终汇聚于一点，因此少了一次for循环，并且不需要处理边界条件

```C++
#include <iostream>
using namespace std;

const int N = 510;
int f[N][N];
int n;

int main(){
    cin >> n;
    for(int i = 1; i <= n; i++)
        // 这里注意是j <= i 而不是j <= n
        for(int j = 1; j <= i; j++)
            cin >> f[i][j];
    for(int i = n; i >= 1; i--){
        for(int j = n; j >= 1; j--){
            f[i][j] += max(f[i + 1][j], f[i + 1][j + 1]);
        }
    }
    
    cout << f[1][1] << endl;
}
```

**时间复杂度：**$O(n^2)$

**空间复杂度：**$O(n^2)$

#### 最长上升子序列

##### 朴素版

```C++
#include <iostream>
using namespace std;

const int N = 1010;

// 状态表达式，f[i]表示第i个元素结尾的所有单调递增子序列的集合
// f[i]的值表示这些集合的序列最长度
int f[N];
// 存储序列
int a[N];
int n;

int main(){
    cin >> n;
    for(int i = 1; i <= n; i++)
        cin >> a[i];
    // res用于获取每个元素结尾的最大单调递增子序列长度
    int res = 1;
    for(int i = 1; i <= n; i++){
        f[i] = 1;
        // 遍历前j个元素，每个元素结尾的最大单调递增子序列长度为f[j]
        for(int j = 1; j < i; j++){
            // 如果第i个元素比第j个元素大，说明第i个元素可以加入该递增子序列，则长度加1
            if(a[i] > a[j]) f[i] = max(f[i], f[j] + 1);
        }
        res = max(res, f[i]);
    }
    
    cout << res << endl;
}
```

**时间复杂度：**$O(n^2)$

**空间复杂度：**$O(n)$



##### 优化版

动态规划 + 二分查找

```C++
#include <iostream>
using namespace std;

const int N = 1010;
// tails[i]记录序列尾部元素, len表示当前序列的长度
int tails[N], len;
int a[N];
int n;

int main(){
    cin >> n;
    for(int i = 1; i <= n; i++)
        cin >> a[i];
    // 初始化tails
    tails[++len] = a[1];
    // 从第二个元素开始遍历
    for(int i = 2; i <= n; i++){
        // 如果当前元素比序列尾部元素大，说明该元素可直接纳入该序列，序列长度加一
        if(a[i] > tails[len]) 
            tails[++len] = a[i];
        // 如果当前元素比序列尾部元素小，根据元素之间差距越小序列长度越可能长的原理
        // 找到序列中比当前元素大的第一个元素，将其取代为当前元素a[i]
        // 序列长度不变
        else{
            int l = 1, r = len;
            while(l < r){
                int mid = (l + r) >> 1;
                if(tails[mid] >= a[i]) 
                    r = mid;
                else 
                    l = mid + 1;
            }
            tails[l] = a[i];
        }
    }
    // 最后输出len，len为最长子序列长度
    cout << len << endl;
}
```

**时间复杂度：**$O(nlog(n))$

**空间复杂度：**$O(n)$

#### 最长公共子序列

##### 朴素版

题解详见：https://www.acwing.com/solution/content/8111/

```C++
#include <iostream>
using namespace std;

const int N = 1010, M = 1010;
int f[N][M];
int n, m;
char a[N], b[M];

int main(){
    cin >> n >> m >> a + 1 >> b + 1;
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            f[i][j] = max(f[i - 1][j], f[i][j - 1]);
            if(a[i] == b[j]) f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
        }
    }
    
    cout << f[n][m] << endl;
}
```

## 区间DP

https://www.acwing.com/problem/content/284/

```

```



## 小技巧

### memset

> 详见https://blog.csdn.net/Supreme7/article/details/115431235

```
#include <cstring>
void *memset(void *str, int c, size_t n)
```

给前n个字节赋值c	

常用

- 初始化为0:：memset(a,-1,sizeof(a))
- 初始化为-1：memset(a,0,sizeof(a))
- 初始化为无穷大：memset(a,0x3f,sizeof(a))

### 无穷大

> 详见 https://blog.csdn.net/qq_40816078/article/details/82459599#:~:text=准确的说：%20inf

一般设置无穷大为INF=0x3f3f3f3f

初始化为无穷大：memset(a,0x3f,sizeof(a))

## STL容器

### 优先级队列

https://blog.csdn.net/weixin_36888577/article/details/79937886