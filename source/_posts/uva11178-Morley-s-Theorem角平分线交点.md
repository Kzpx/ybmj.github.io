---
title: uva11178-Morley's Theorem角平分线交点
comments: true
date: 2018-08-02 08:06:50
categories:
- ACM
- 数学
- 计算几何
- 二维几何
---

## 题意
Morley定理：作三角形ABC每个内角的三等分线，相交成三角形DEF，则DEF是等边三角形。

任务是根据A，B，C三点的位置，确定D，E，F的位置。

保证A，B，C面积非0，且按逆时针给出。
## 分析
1. 求出一个内角
2. 将某条边旋转1/3倍的内角
3. 然后求两直线交点

代码复用要好好学学
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
struct Point {
    double x, y;
    Point(double x = 0, double y = 0) : x(x), y(y) {}
};
typedef Point Vec;
Vec operator+(Vec A, Vec B) { return Vec(A.x + B.x, A.y + B.y); }
Vec operator-(Vec A, Vec B) { return Vec(A.x - B.x, A.y - B.y); }
Vec operator*(Vec A, double p) { return Vec(A.x * p, A.y * p); }
Vec operator/(Vec A, double p) { return Vec(A.x / p, A.y / p); }
double Cross(Vec A, Vec B) { return A.x * B.y - A.y * B.x; }
double Dot(Vec A, Vec B) { return A.x * B.x + A.y * B.y; }  // 点积
double Length(Vec A) { return sqrt(Dot(A, A)); }            // 向量长度
double Angle(Vec A, Vec B) {                                // 两向量夹角
    return acos(Dot(A, B) / Length(A) / Length(B));
}
Vec Rotate(Vec A, double rad) {  // 向量绕起点逆时针旋转rad
    return Vec(A.x * cos(rad) - A.y * sin(rad),
               A.x * sin(rad) + A.y * cos(rad));
}
// 调用前要确保直线P + tv和Q + tw有唯一交点，当且仅当Cross(v,w)!=0
Point GetLineItersection(Point P, Vec v, Point Q, Vec w) {
    Vec u = P - Q;
    double t = Cross(w, u) / Cross(v, w);
    return P + v * t;
}

Point p[5];

Point GetP(int a, int b, int c) {
    Vec v1 = p[c] - p[b];
    double ang = Angle(p[a] - p[b], v1);
    v1 = Rotate(v1, ang / 3);

    Vec v2 = p[a] - p[c];
    ang = Angle(p[b] - p[c], v2);
    v2 = Rotate(v2, 2 * ang / 3);

    return GetLineItersection(p[b], v1, p[c], v2);
}
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
        for (int i = 0; i < 3; i++) {
            scanf("%lf%lf", &p[i].x, &p[i].y);
        }
        Point D, E, F;
        D = GetP(0, 1, 2);
        E = GetP(1, 2, 0);
        F = GetP(2, 0, 1);
        printf("%.6lf %.6lf %.6lf %.6lf %.6lf %.6lf\n", D.x, D.y, E.x, E.y, F.x,
               F.y);
    }
}
```