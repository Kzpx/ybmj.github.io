---
title: cf528D-Fuzzy Search
comments: true
date: 2018-08-28 09:43:07
categories:
- ACM
- 数学
- FFT
---

## 题意
给你两个串a,b,和一个数字k, 对于b[i]  它可以匹配 a中[i-k,i+k]任意一个位置的字符（a中每个字符可以重复匹配）。

问b在a中可以有几次匹配？

$1 \leq b \leq a \leq 2e5, 0 \leq k \leq 2e5$
## 分析

如果 b[i] 可以匹配 a[i-k,i+k], 不妨把a[i-k,i+k]的字符都放到 a[i]这个位置上（前提是ａ中每个字符是可以重复匹配的）。

这样的话问题又变成了传统套路题。
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
#define first fi
#define second se
#define my_unique(a) a.resize(distance(a.begin(), unique(a.begin(), a.end())))
#define my_sort_unique(c) (sort(c.begin(), c.end())), my_unique(c)
typedef long long ll;
typedef pair<int, int> pii;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e6 + 5;
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
int ok[maxn][5];
int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    map<char, int> id;
    id['A'] = 1;
    id['G'] = 2;
    id['C'] = 3;
    id['T'] = 4;
    int len1, len2, k;
    scanf("%d%d%d%s%s", &len1, &len2, &k, a, b);
    int len = 1;
    while (len < len1 * 2 || len < len2 * 2) len <<= 1;
    deque<int> dq;
    for (int i = 0; i < k && i < len1; i++) {
        dq.push_back(id[a[i]]);
        sum[id[a[i]]]++;
    }
    for (int i = 0; i < len1; i++) {
        if (i + k < len1) dq.pb(id[a[i + k]]), sum[id[a[i + k]]]++;
        if (i > k) {
            sum[dq.front()]--;
            dq.pop_front();
        }
        for (int j = 1; j < 5; j++)
            if (sum[j]) ok[i][j]++;
    }
    for (int i = 1; i < 5; i++) sum[i] = 0;
    for (int c = 1; c < 5; c++) {
        for (int i = 0; i < len; i++)
            x1[i] = Complex(0, 0), x2[i] = Complex(0, 0);
        for (int i = 0; i < len1; i++)
            if (ok[i][c]) x1[i] = Complex(1, 0);
        for (int i = 0; i < len2; i++)
            if (id[b[i]] == c) x2[len2 - i - 1] = Complex(1, 0);
        fft(x1, len, 1);
        fft(x2, len, 1);
        for (int i = 0; i < len; i++) x1[i] = x1[i] * x2[i];
        fft(x1, len, -1);
        for (int i = 0; i < len; i++) sum[i] += (int)(x1[i].x + 0.5);
    }
    ll ans = 0;
    for (int i = len2 - 1; i < len1; i++) {
        if (sum[i] == len2) ans++;
    }
    printf("%lld\n", ans);
}
```
