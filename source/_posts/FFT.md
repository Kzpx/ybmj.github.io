---
title: FFT
comments: true
date: 2018-08-26 17:10:41
categories:
- ACM
- 数学
- 快速变换
- FFT
---

# FFT

离散傅里叶变化主要是用来做信号分析的，ACM中主要用来加速求多项式乘积

[学习博客](https://zhuanlan.zhihu.com/p/40505277)


### 系数表示法与点值表示法

$f(x) = ax^2 + bx^1 + c$

系数表示法 $f(x) = {a,b,c}$

点值表示法 $f(x) = \{\{x_0,f(x_0)\},\{x_1,f(x_1)\},\{x_2, f(x_2)\} \}$

(n个未知数的方程可以用n个解求得)

**DFT** : 多项式由系数表示法 转为 点值表示法的过程

**IDFT** : 多项式由点值表示法 转为 系数表示法的过程

如果我要求一个多项式 $F(x) = f(x) \times g(x)$

可以用点值表示法来表示 

$F(x) = \{ \{x_0, f(x_0) \times g(x_0)\}, \{x_1,f(x_1) \times g(x_1) \}... \}$

至此如何求一个多项式的乘积我们大概有了思路：
1. 将f(x) 与g(x) 转化为点值表达式， 然后对应项相乘，得到F(x)的点值表达式
2. 将F(x)转化为系数表达式

### DFT
怎么将点值表达式转化为系数表达式呢？

这里引入了单位复根，使得不用去计算$x_i$，从而加速了DFT的过程。 

只要知道 单位复根的多少次幂都是1即可。
由于还不会高斯消元..所以这部分先搁置一下。

### 分治思想加速DFT

FFT快在它利用了分治的思想加速了DFT的过程，即第一步.

对于一个多项式$f(x) = y_0 = a_0 + a_1x +a_2x^2+a_3x^3 + a_4x^4 + a_5x^5$

然后将奇偶项分开处理

$f(x) = (a_0 + a_2x^2 + a_4x^4) + x(x_1 + a_3x^2 + a_5x^4)$

设
$G(x) = a_0 + a_2x + a_4x^2$
$H(x) = a_1 + a_3x + a_5x^2$

那么$f(x) = G(x^2) + xH(x^2)$

需要注意的是$f(x)$的项数必须为2的幂次，不然在分治的时候会造成奇偶项数不相等. 所以我们需要提前预处理，将$f(x)$的项数扩大到2的幂次，多余的项数补0.

### IDFT
看博客吧..

## 模板
```cpp
// ybmj
#include <bits/stdc++.h>
using namespace std;
#define lson (rt << 1)
#define rson (rt << 1 | 1)
#define lson_len (len - (len >> 1))
#define rson_len (len >> 1)
#define pb(x) push_back(x)
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
#define first fi
#define second se
#define my_unique(a) a.resize(distance(a.begin(), unique(a.begin(), a.end())))
#define my_sort_unique(c) (sort(c.begin(), c.end())), my_unique(c)
typedef long long ll;
typedef pair<int, int> pii;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 2e5 + 5;
const double PI = acos(-1.0);
//复数结构体
struct Complex {
    double x, y;  //实部和虚部 x+yi
    Complex(double _x = 0.0, double _y = 0.0) { x = _x, y = _y; }
    Complex operator-(const Complex& b) const {
        return Complex(x - b.x, y - b.y);
    }
    Complex operator+(const Complex& b) const {
        return Complex(x + b.x, y + b.y);
    }
    Complex operator*(const Complex& b) const {
        return Complex(x * b.x - y * b.y, x * b.y + y * b.x);
    }
};
/*
* 进行FFT和IFFT前的反转变换。
* 位置i和 （i二进制反转后位置）互换
* len必须取2的幂
*/
void change(Complex y[], int len) {
    for (int i = 1, j = len / 2; i < len - 1; i++) {
        if (i < j) swap(y[i], y[j]);
        //交换互为小标反转的元素，i<j保证交换一次
        // i做正常的+1，j左反转类型的+1,始终保持i和j是反转的
        int k = len / 2;
        while (j >= k) j -= k, k /= 2;
        if (j < k) j += k;
    }
}
/*
* 做FFT
* len必须为2^k形式，
* on==1时是DFT，on==-1时是IDFT
* DFT:系数表示法->点值表示法
* IDFT:点值表示法->系数表示法
*/
void fft(Complex y[], int len, int on) {
    change(y, len);
    for (int h = 2; h <= len; h <<= 1) {
        Complex wn(cos(-on * 2 * PI / h), sin(-on * 2 * PI / h));
        // 计算当前的单位复根
        for (int j = 0; j < len; j += h) {
            Complex w(1, 0);
            // 计算当前的单位复根
            for (int k = j; k < j + h / 2; k++) {
                Complex u = y[k];
                Complex t = w * y[k + h / 2];
                y[k] = u + t, y[k + h / 2] = u - t;
                w = w * wn;
            }
        }
    }
    if (on == -1)
        for (int i = 0; i < len; i++) y[i].x /= len;
}
Complex x1[maxn], x2[maxn];
char a[maxn], b[maxn];
int sum[maxn];
int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    while (~scanf("%s%s", &a, &b)) {
        int len1 = strlen(a);
        int len2 = strlen(b);
        int len = 1;
        while (len < len1 * 2 || len < len2 * 2)
            len <<= 1;  // len > len1 + len2
        for (int i = 0; i < len1; i++)
            x1[i] = Complex(a[len1 - 1 - i] - '0', 0);
        for (int i = len1; i < len; i++) x1[i] = Complex(0, 0);
        for (int i = 0; i < len2; i++)
            x2[i] = Complex(b[len2 - 1 - i] - '0', 0);
        for (int i = len2; i < len; i++) x2[i] = Complex(0, 0);
        fft(x1, len, 1);
        fft(x2, len, 1);
        for (int i = 0; i < len; i++) x1[i] = x1[i] * x2[i];
        fft(x1, len, -1);
        for (int i = 0; i < len; i++) {
            sum[i] = int(x1[i].x + 0.5);
        }
        while (sum[len] == 0 && len > 0) len--;
        for (int i = len; i >= 0; i--) printf("%d", sum[i]);
        puts("");
    }
}
```