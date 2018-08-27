---
title: hdu5885-XM Reserves
comments: true
date: 2018-08-27 22:41:29
categories:
- ACM
- 数学
- FFT
---

## 题意
n*m的矩形，每个点有p(i,j)，你可以选择一个点(x,y)，那么在该点可以得到的价值val为
```cpp
rep(i,1,n) rep(j,1,m) {
    dis=(i,j)与(x,y)欧几里得距离
    if(dis<R) val+=p(i,j)/(dis+1)
}
```
要求选取一个点，使val最大，输出val

(1≤n,m≤500), (0≤r≤300).
## 分析
FFT建模

首先观察这个式子$$\frac{p(i,j)}{\sqrt{(dx^2+dx^2)} + 1}$$

它对$(i + dx, j + dy)$有贡献

发现分子分母之间没有联系，所以是可以分开的。

不妨设$A[i][j] = p(i,j)$, $B[dx][dy] = \frac{1}{\sqrt{(dx^2+dx^2)} + 1}$

所以$C[i+dx][j+dy] = A[i][j] * B[dx][dy]$

这个卷积式已经够明显了吧。

接下来要做的就是降维。

dx的范围是[-r,r] , dy的范围是[-r,r]. 

所以需要进行下标的偏移，行的长度变为 m + 2*r, 列的长度变为 n + 2*r.

注意限制条件 $dx^2 + dy^2 < R^2$
## 代码
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
#define my_unique(a) a.resize(distance(a.begin(), unique(a.begin(), a.end())))
#define my_sort_unique(c) (sort(c.begin(), c.end())), my_unique(c)
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef pair<double, double> pdd;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;

const int maxn = 3e6+6;
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

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n, m;
    double R;
    while (~scanf("%d%d%lf", &n, &m, &R)) {
        int r = ceil(R);
        int M = max(n, m) + 2 * r;
        int len = 1;
        while (len < M * M * 2) len <<= 1;
        for (int i = 0; i < len; i++)
            x1[i] = Complex(0, 0), x2[i] = Complex(0, 0);
        double u;
        for (int i = 0; i < n; i++) {
            for (int k = 0; k < m; k++)
                scanf("%lf", &u), x1[i * M + k] = Complex(u, 0);
        }
        for (int i = -r; i <= r; i++) {
            for (int k = -r; k <= r; k++) {
                if (i * i + k * k < R * R)
                    x2[(i + r) * M + k + r] =
                        Complex(1.0 / (sqrt(i * i + k * k) + 1), 0);
            }
        }
        fft(x1, len, 1);
        fft(x2, len, 1);
        for (int i = 0; i < len; i++) x1[i] = x1[i] * x2[i];
        fft(x1, len, -1);
        double ans = 0;
        for (int i = 0; i < n; i++) {
            for (int k = 0; k < m; k++) {
                ans = max(ans, x1[(i + r) * M + r + k].x);
            }
        }
        printf("%.3lf\n", ans);
    }
}
```
