# 算法基础

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

##### 朴素版

```c++
#include <iostream>
using namespace std;

const int N = 110;
int v[N], w[N], s[N];
int f[N][N];

int main(){
    int n,m;
    cin >> n >> m;
    for(int i = 1;i <= n;i++){
        cin >> v[i] >> w[i] >> s[i];
    }
    for(int i = 1;i <= n;i++){
        for(int j = 0;j <= m;j++){
            for(int k = 0;k <= s[i]&&k*v[i]<=j;k++){
                f[i][j] = max(f[i][j],f[i-1][j-k*v[i]]+k*w[i]);
            }
        }
    }
    
    cout << f[n][m] << endl;
}
```

##### 优化版

```c++
#include <iostream>
using namespace std;

const int N = 12010, V = 2010;
int v[N], w[N], s[N];
int f[V];

int main(){
    int n,m,cnt=0;
    cin >> n >> m;
    for(int i = 1;i <= n;i++){
        int a, b, s;
        cin >> a >> b >> s;
        int k = 1;
        while(k<=s){
            cnt ++;
            v[cnt] = a*k;
            w[cnt] = b*k;
            s -= k;
            k *= 2;
        }
        if(s>0){
            cnt++;
            v[cnt] = a*s;
            w[cnt] = b*s;
        }
    }
    for(int i = 1;i <= cnt;i++){
        for(int j = m;j >= v[i];j--){
            f[j] = max(f[j],f[j-v[i]]+w[i]);
        }
    }
    
    cout << f[m] << endl;
}
```



#### 分组背包问题

```c++
#include <iostream>
using namespace std;

const int N = 110;
int v[N][N],w[N][N],s[N];
int f[N][N];

int main(){
    int n, m;
    cin >> n >> m;
    for(int i = 1;i <= n;i++){
        cin >> s[i];
        for(int j = 1;j <= s[i];j++){
            cin >> v[i][j] >> w[i][j];
        }
    }
    for(int i = 1;i <= n;i++){
        for(int j = 0;j <= m;j++){
            f[i][j] = f[i-1][j];
            for(int k = 1;k <= s[i];k++){
                if(j>=v[i][k]) f[i][j] = max(f[i][j],f[i-1][j-v[i][k]]+w[i][k]);
            }
        }
    }
    
    cout << f[n][m] << endl;
}
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