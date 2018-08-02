---
title: uva11796-Dog Distance-两点运动时的最短距离与最长距离
comments: true
date: 2018-08-02 08:20:55
categories:
- ACM
- 数学
- 计算几何
- 二维几何
---

## 题意
甲和乙两条狗分别沿着不同的折线奔跑。两只狗的速度未知，但已知它们同时出发，同时到达，并且都是匀速奔跑。

你的任务是求出甲和乙在奔跑过程中的最远距离和最近距离的差。

## 分析
先将问题简化，甲和乙的路线是一条线段。 因为运动是相对的，所以可以把甲看做静止，这样问题就转化成了点到线段的最短距离与最长距离。

接下来要做的就是模拟整个过程。设现在甲的位置是Pa，刚经过编号为Sa的拐点； 乙同理。

那么我们只需要谁先到达拐点，在这个时间点之前的问题其实就是问题的简化版。

求解完后要分别更新甲和乙的位置，如果正好到达下一个拐点，还要更新Sa或Sb。 

时间复杂度O(n)
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
const int maxn = 100;
const double eps = 1e-10;
struct Point {
    double x, y;
    Point(double x = 0, double y = 0) : x(x), y(y) {}
};
typedef Point Vec;

Vec operator+(Vec A, Vec B) { return Vec(A.x + B.x, A.y + B.y); }
Vec operator-(Vec A, Vec B) { return Vec(A.x - B.x, A.y - B.y); }
Vec operator*(Vec A, double p) { return Vec(A.x * p, A.y * p); }
Vec operator/(Vec A, double p) { return Vec(A.x / p, A.y / p); }
bool operator<(const Point& a, const Point& b) {
    return a.x < b.x || (a.x == b.x && a.y < b.y);
}
int dcmp(double x) {
    if (fabs(x) < eps)
        return 0;
    else
        return x < 0 ? -1 : 1;
}
bool operator==(const Point& a, const Point& b) {
    return dcmp(a.x - b.x) == 0 && dcmp(a.y - b.y) == 0;
}
double PolarAngle(Point A) { return atan2(A.y, A.x); }  // 极角

double Dot(Vec A, Vec B) { return A.x * B.x + A.y * B.y; }  // 点积
double Length(Vec A) { return sqrt(Dot(A, A)); }            // 向量长度
double Angle(Vec A, Vec B) {                                // 两向量夹角
    return acos(Dot(A, B) / Length(A) / Length(B));
}
double Cross(Vec A, Vec B) { return A.x * B.y - A.y * B.x; }
double DistanceToSegment(Point P, Point A, Point B) {
    if (A == B) return Length(P - A);
    Vec v1 = B - A, v2 = P - A, v3 = P - B;
    if (dcmp(Dot(v1, v2)) < 0)
        return Length(v2);
    else if (dcmp(Dot(v1, v3)) > 0)
        return Length(v3);
    else
        return fabs(Cross(v1, v2)) / Length(v1);
}
Point a[maxn], b[maxn];
double Min, Max;
void update(Point P, Point A, Point B) {
    Min = min(Min, DistanceToSegment(P, A, B));
    Max = max(Max, Length(P - A));
    Max = max(Max, Length(P - B));
}
int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int T, kase = 1;
    scanf("%d", &T);
    while (T--) {
        int A, B;
        scanf("%d%d", &A, &B);
        for (int i = 0; i < A; i++) scanf("%lf%lf", &a[i].x, &a[i].y);
        for (int i = 0; i < B; i++) scanf("%lf%lf", &b[i].x, &b[i].y);
        double LenA = 0, LenB = 0;
        for (int i = 0; i < A - 1; i++) LenA += Length(a[i + 1] - a[i]);
        for (int i = 0; i < B - 1; i++) LenB += Length(b[i + 1] - b[i]);
        int Sa = 0, Sb = 0;
        Point Pa = a[0], Pb = b[0];
        Min = 1e9, Max = 1e-9;
        while (Sa < A - 1 && Sb < B - 1) {
            double La = Length(a[Sa + 1] - Pa);
            double Lb = Length(b[Sb + 1] - Pb);
            double T = min(La / LenA, Lb / LenB);   // 速度假设为LenA，LenB
            Vec Va = (a[Sa + 1] - Pa) / La * T * LenA;  // 位移向量
            Vec Vb = (b[Sb + 1] - Pb) / Lb * T * LenB;
            update(Pa, Pb, Pb + Vb - Va);
            Pa = Pa + Va;
            Pb = Pb + Vb;
            if (Pa == a[Sa + 1]) Sa++;
            if (Pb == b[Sb + 1]) Sb++;
        }
        printf("Case %d: %.0f\n", kase++, Max - Min);
    }
}
```