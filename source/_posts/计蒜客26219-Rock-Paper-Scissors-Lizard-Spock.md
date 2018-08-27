---
title: 计蒜客26219-Rock Paper Scissors Lizard Spock
comments: true
date: 2018-08-27 22:09:24
categories:
- ACM
- 数学
- FFT
---

## 题意
给两个只包含大写字母的字符串$s1,s2, (1 \leq s2 \leq s1 \leq 10^6)$，以及一些克制关系。

你需要从$s1$中找一子串$t$，使得$t$与$s2$匹配所得分数最大。

匹配的意思是 :
```cpp
for(i : 0 -> t.size())
    if(s2[i] 克制 t[i]) 分数++
```

## 分析
FFT解决字符串匹配的套路题

枚举克制关系

比如A克制B， 那么我将$s1$中所有字符为B的位置都设为1，其余为0。 再将$s2$中所有字符为A的位置都设为1，其余为0

然后跑一遍fft，得到的结果就是sum[i] 就是$s1$中以第i个位置结尾的子串与$s2$的在当前克制关系下的分数。

然后枚举克制关系，把所有的分数加起来，最后再扫一遍取最大值即可（注意i的范围是[s2-1, s1-1])

## 代码
```cpp
//ybmj
#include<bits/stdc++.h>
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
typedef pair<int,int> pii;
typedef pair<ll,ll> pll;
typedef pair<double,double> pdd;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;

const int maxn = 3e6 + 5;
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
char a[maxn],b[maxn];
int sum[maxn];
int main(){
	// /*
	#ifndef ONLINE_JUDGE
	freopen("1.in","r",stdin);
	freopen("1.out","w",stdout);
	#endif
	// */
	std::ios::sync_with_stdio(false);
	while(~scanf("%s%s",a,b)){
		map<char,pair<char,char> > hash;
		hash['R'] = mp('K','P');
		hash['L'] = mp('R','S');
		hash['K'] = mp('L','P');
		hash['P'] = mp('L','S');
		hash['S'] = mp('R','K');
		int len1 = strlen(a);
		int len2 = strlen(b);
		int len = 1;
		while(len < len1 * 2 || len < len2 * 2) len <<= 1;
		for(int i=0;i<len;i++) sum[i] = 0;
		for(auto &u:hash){
			for(int i=0;i<len;i++) x1[i] = Complex(0,0), x2[i] = Complex(0,0);
			for(int i=0;i<len1;i++) 
				if(a[i] == u.first) x1[len1 - i - 1] = Complex(1,0);
			for(int i=0;i<len2;i++)
				if(b[i] == u.second.first || b[i] == u.second.second) x2[i] = Complex(1,0);
			fft(x1,len,1);
			fft(x2,len,1);
			for(int i=0;i<len;i++) x1[i] = x1[i] * x2[i];
			fft(x1,len,-1);
			for(int i=0;i<len;i++)
				sum[i] += int(x1[i].x + 0.5);
		}
		int ans = 0;
		for(int i=0;i<len;i++) ans = max(ans, sum[i]);
		printf("%d\n",ans);
	}
}
```
