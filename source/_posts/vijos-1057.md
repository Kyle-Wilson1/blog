---
title: Vijos-1057题解【动态规划】
date: 2018-03-18 07:56:25
categories: 动态规划
tags: [算法,vijos]
---
### 题目出处：
<a href="https://www.vijos.org/p/1057" target="_blank"><font color=blue>https://www.vijos.org/p/1057</font></a>
### 题目描述
给一个N*M的土地，由0和1表示，0表示瑕疵，1表示完好，找出最大的完好的正方形土地？
<!--more-->
### 输入
输入文件第一行为两个整数n,m（1<=n,m<=1000），接下来n行，每行m个数字，用空格隔开。0表示该块土地有瑕疵，1表示该块土地完好
4 4
0 1 1 1
1 1 1 0
0 1 1 0
1 1 0 1
### 思路分析
首先看数据范围，1~1000，存储地图需要O(n\*m)的内存空间，空间复杂度可控；再看时间复杂度，遍历所有点为O(n\*m)；
为什么要先分析空间及时间复杂度呢？因为很多时候，数据规模就决定了算法，如果数据规模为1~1000000000，你还会考虑O(n*n)的算法吗，肯定不会，这种数据一看就知道最优解法为线性复杂度O(n)的算法；
列举如下3处情况：图中彩色方框都为1，白色方框都为0
<img src="../../../../images/vijos-1057/1057-1.png" width="100%">假设地图中存在一个最大的正方形，则该正方形存在一个右下角P点，且该点为1；
如果当某一个点P(i,j)为0时，则以该点为右下角不存在正方形，所以该点也不可能在最大的正方形中。
#### 不同情况列举：
1. 图P1.1
当P(i,j)=1时，可以看出以P为右下角，最优解由红色边长决定，即以P点为起点，向左连续为1的最多个数，木桶原理
2. 图P1.2
当P(i,j)=1时，可以看出以P为右下角，最优解由紫色边长决定，即以M点为右下角能组成的最大的正方形
3. 图P1.3
当P(i,j)=1时，可以看出以P为右下角，最优解由绿色边长决定，即以P点为起点，向上连续为1的最多个数

则可以建立如下DP公式
设f[i][j]：以点(i,j)为右下角，能组成的最大正方形边长
left[i][j]：以点(i,j)为起点，向左最大连续1的个数，提前初始化
up[i][j]：以点(i,j)为起点，向上最大连续1的个数，提前初始化
f[i][j]=max(f[i-1][j-1]+1, left[i][j], up[i][j])
注：地图为一行或者一列时，提前预处理，方便后面递推
### C++源码如下：
github: <a href="https://github.com/Kyle-Wilson1/Vijos/tree/master/P1057" target="_blank"><font color=blue>https://github.com/Kyle-Wilson1/Vijos/tree/master/P1057</font></a>
```cpp
#include <iostream>
#include <fstream>
#include <vector>

using namespace std;

struct Node {
    int left, up;
};

int main() {
    ifstream cin("a.in");
    ofstream cout("a.out");

    vector<vector<int>> f(1000, vector<int>(1000, 0));
    vector<vector<int>> map(1000, vector<int>(1000, 0));
    Node initNode{0, 0};
    vector<vector<Node>> node(1000, vector<Node>(1000, initNode));

    int n, m, i, j, maxSquare = 0;

    cin >> n >> m;
    //input
    for (i = 0; i < n; i++)
        for (j = 0; j < m; j++)
            cin >> map[i][j];

    //init left
    for (i = 0; i < n; i++)
        for (j = 0; j < m; j++) {
            if (j == 0) {
                if (map[i][j] == 1) {
                    node[i][j].left = 1;
                } else { node[i][j].left = 0; }
            } else {
                if (map[i][j] == 1) {
                    node[i][j].left = node[i][j - 1].left + 1;
                } else {
                    node[i][j].left = 0;
                }
            }
        }

    //init up
    for (j = 0; j < m; j++)
        for (i = 0; i < n; i++) {
            if (i == 0) {
                if (map[i][j] == 1) {
                    node[i][j].up = 1;
                } else { node[i][j].up = 0; }
            } else {
                if (map[i][j] == 1) {
                    node[i][j].up = node[i - 1][j].up + 1;
                } else {
                    node[i][j].up = 0;
                }
            }
        }

    auto maxOfTwo = [](int a, int b) { return a > b ? a : b; };
    auto minOfThree = [](int a, int b, int c) { return a < b ? a < c ? a : c : b < c ? b : c; };

    //dynamic programming
    for (i = 0; i < n; i++) {
        if (map[i][0] == 1) {
            f[i][0] = 1;
            maxSquare = 1;
        } else f[i][0] = 0;
    }

    for (j = 1; j < m; j++) {
        if (map[0][j] == 1) {
            f[0][j] = 1;
            maxSquare = 1;
        } else f[0][j] = 0;
    }

    for (i = 1; i < n; i++)
        for (j = 1; j < m; j++) {
            f[i][j] = minOfThree(node[i][j].left, node[i][j].up, f[i - 1][j - 1] + 1);
            maxSquare = maxOfTwo(f[i][j], maxSquare);
        }

    cout << maxSquare;
    cin.close();
    cout.close();
    return 0;
}
```

