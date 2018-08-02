---
title: uvalive3263-That Nice Euler Circuits-欧拉定理
comments: true
date: 2018-08-02 08:12:25
categories:
- ACM
- 数学
- 计算几何
- 二维几何
---

## 题意
平面上有一个包含n个端点的一笔画，第n个端点总是与第一个端点重合，因此图案是一条闭合的折线。
线段之间可以相交，但是不会重叠。
求这些线段将平面分成几个部分？

## 分析
欧拉定理：顶点数 + 面数 = 边数 + 2

那么只要求顶点数和边数即可。

需要注意的是：
1. 对于重复的点不能算，比如三线共点
2. 点增加了，边也会增加。

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
const double eps = 1e-10;
const int maxn = 305;
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
bool SegmentProperIntersection(Point a1, Point a2, Point b1, Point b2) {
    double c1 = Cross(a2 - a1, b1 - a1), c2 = Cross(a2 - a1, b2 - a1),
           c3 = Cross(b2 - b1, a1 - b1), c4 = Cross(b2 - b1, a2 - b1);
    return dcmp(c1) * dcmp(c2) < 0 && dcmp(c3) * dcmp(c4) < 0;
}
Point GetLineItersection(Point P, Vec v, Point Q, Vec w) {
    Vec u = P - Q;
    double t = Cross(w, u) / Cross(v, w);
    return P + v * t;
}
bool OnSegment(Point p, Point a1, Point a2) {
    return dcmp(Cross(a1 - p, a2 - p)) == 0 && dcmp(Dot(a1 - p, a2 - p)) < 0;
}

Point p[maxn];
int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n, kase = 1;
    while (~scanf("%d", &n)) {
        if (!n) break;
        set<Point> vtx;
        for (int i = 0; i < n; i++) {
            scanf("%lf%lf", &p[i].x, &p[i].y);
            vtx.insert(p[i]);
        }
        for (int i = 0; i < n - 1; i++) {
            for (int k = i + 1; k < n - 1; k++) {
                if (SegmentProperIntersection(p[i], p[i + 1], p[k], p[k + 1])) {
                    vtx.insert(GetLineItersection(p[i], p[i + 1] - p[i], p[k],
                                                  p[k + 1] - p[k]));
                }
            }
        }
        int e = n - 1;
        for (auto i : vtx) {
            for (int k = 0; k < n - 1; k++) {
                if (OnSegment(i, p[k], p[k + 1])) e++;
            }
        }
        int ans = e + 2 - vtx.size();
        printf("Case %d: There are %d pieces.\n", kase++, ans);
    }
}
```