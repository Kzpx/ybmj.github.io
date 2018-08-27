---
title: hdu4609-3-idiots
comments: true
date: 2018-08-27 22:33:55
categories:
- ACM
- 数学
- FFT
---

## 题意
给定n个长度为ai的木棍求，任选三个使其能够组成三角形的概率

$(3≤n≤10^5)$. $(1≤a_i≤105)$

## 分析
长度比较小，考虑$c[i]$表示长度为i的木棒的个数。$d[i + k] = c[i] * c[k]$ 表示两根木棒组合长度为i+k的种数。

这样可以通过枚举第三边，求得答案。

问题是如何求得d数组， 可以观察到这是一个卷积的形式， 所以可以FFT加速一下。

这些方案里需要去除一些不满足要求(ai为最长边)的 （先排序）

1. 另外两条边两条均>ai，ans-=(n-i)*(n-i-1)/2
2. 另外两条边一条>ai，一条<ai,ans-=(n-i)*(i-1)
3. 另外两条边一条=ai，另一条随意,ans-=n-1
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
const int maxn = 1e5 + 5;
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
Complex x[maxn << 2];
ll sum[maxn << 2], c[maxn], a[maxn], pre[maxn << 2];
int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int T;
    scanf("%d", &T);
    while (T--) {
        int n;
        scanf("%d", &n);
        clr(sum,0);
        for (int i = 0, x; i < n; i++) scanf("%lld", a + i), ++sum[a[i]];
        sort(a, a + n);
        int len1 = a[n-1] + 1;
        int len = 1;
        while (len < len1 * 2)
            len <<= 1;  // len > len1 + len2
        for (int i = 0; i < len1; i++) x[i] = Complex(sum[i], 0);
        for (int i = len1; i < len; i++) x[i] = Complex(0, 0);
        fft(x, len, 1);
        for (int i = 0; i < len; i++) x[i] = x[i] * x[i];
        fft(x, len, -1);
        for (int i = 0; i < len; i++) {
            sum[i] = ll(x[i].x + 0.5);
        }
        len = a[n-1] * 2;
        for(int i=0;i<n;i++) sum[a[i] << 1]--;
        for(int i=0;i<=len;i++) sum[i] >>= 1;        
        pre[0] = sum[0];
        for (int i = 1; i <= len; i++) {
            pre[i] = pre[i - 1] + sum[i];
        }
        ll ans = 0;
        for (int i = 0; i < n; i++) {
            ans += pre[len] - pre[a[i]];
            ans -= n - 1;
            ans -= 1LL * (n - i - 1) * (i);
            ans -= 1LL * (n - i - 1) * (n - i - 2) / 2;
        }
        ll tmp = 1LL * (n) * (n-1) * (n-2) / 6;
        printf("%.7lf\n",1.0 * ans/tmp);
    }
}
```
