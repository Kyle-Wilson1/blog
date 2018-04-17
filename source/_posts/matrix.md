---
title: 递推优化-矩阵快速幂
date: 2018-04-12 16:07:04
categories: 递推
tags: [算法,poj]
---
### 一、整数快速幂
首先我们抛出一个问题，如何快速求出a<sup>b</sup>，a, b都为正整数？
<!--more-->
复习一下幂运算公式：
① 同底数幂相乘：a^m·a^n=a^(m+n)
② 幂的乘方：(a^m)^n=a^(mn)
③ 积的乘方：(ab)^m=a^m·b^m
④ 同底数幂相除：a^m÷a^n=a^(m-n) (a≠0)
过程如下：
将b转为二进制，比如b=11，则b对应的二进制为1011
① b=2<sup>0</sup>+2<sup>1</sup>+2<sup>3</sup>
② a<sup>b</sup>=a<sup>2<sup>0</sup>+2<sup>1</sup>+2<sup>3</sup></sup>=a<sup>2<sup>0</sup></sup>&times;a<sup>2<sup>1</sup></sup>&times;a<sup>2<sup>3</sup></sup>
③ 2<sup>n</sup>=2<sup>n-1</sup>+2<sup>n-1</sup>
④ a<sup>2<sup>n</sup></sup>=a<sup>2<sup>n-1</sup></sup>&times;a<sup>2<sup>n-1</sup></sup>
```cpp
int pow(int a, int b) {
    int ret = 1;
    while (b) {
        if (b & 1) {
            ret *= a;
        }
        a *= a;
        b >>= 1;
    }
    return ret;
}
```
### 二、矩阵快速幂
① 乘法结合律： (AB)C=A(BC) 
② 乘法左分配律：(A+B)C=AC+BC 
③ 乘法右分配律：C(A+B)=CA+CB
④ 对数乘的结合性k(AB）=(kA)B=A(kB
⑤ 转置 (AB)T=BTAT．
⑥ 矩阵乘法一般不满足交换律
```cpp
// 矩阵乘法
void multiMatrix(int a[][N], int b[][N]) {

    int tmp[N][N] = {0}, i, j;
    for (i = 0; i < N; i++)
        for (j = 0; j < N; j++)
            for (int k = 0; k < N; k++) {
                tmp[i][j] += a[i][k] * b[k][j];
            }

    for (i = 0; i < N; i++)
        for (j = 0; j < N; j++) {
            a[i][j] = tmp[i][j];
        }
}
```
```cpp
// 矩阵求幂
void powMatrix(int a[][N], int b, int ret[][N]) {

    memset(ret, 0, sizeof(int) * N * N);
    for (int i = 0; i < N; i++) {
        ret[i][i] = 1;
    }

    while (b) {
        if (b & 1) {
            multiMatrix(ret, a);
        }
        multiMatrix(a, a);
        b >>= 1;
    }
}
```
### 三、Fibonacci sequence
斐波那契数列指的是这样一个数列 1, 1, 2, 3, 5, 8, 13, 21......
这个数列从第3项开始，每一项都等于前两项之和.
#### 递归求解
```cpp
int solve(int step) {
    if (step == 0 || step == 1) {
        return 1;
    }
    return solve(step - 1) + solve(step - 2);
}
```
#### 递推求解
```cpp
f[0] = 1; f[1] = 1;
for (int i = 2; i < N; i++){
    f[i] = f[i - 1] + f[i - 2]
}
cout << f[N - 1] << endl;
```
#### 矩阵求解
斐波那契数列递推公式很简单，通过前两项求和就可以得到，但数据很大的时候，效率就比较低。因为我们是一直在线性做加法，所以得想办法将加法变成乘法，甚至是幂乘。
递推公式如下：
<a href="http://www.codecogs.com/eqnedit.php?latex=f(n)=f(n-1)&plus;f(n-2)" target="_blank"><img src="http://latex.codecogs.com/gif.latex?f(n)=f(n-1)&plus;f(n-2)" title="P1" /></a>
将递推公式放入矩阵：
<a href="http://www.codecogs.com/eqnedit.php?latex=\left\{&space;\begin{matrix}&space;1&space;&&space;1&space;\\&space;1&space;&&space;0&space;\end{matrix}&space;\right\}&space;\times&space;\dbinom{f(n-1)}{f(n-2)}=&space;\dbinom{f(n-1)&plus;f(n-2)}{f(n-1)}=&space;\dbinom{f(n)}{f(n-1)}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\left\{&space;\begin{matrix}&space;1&space;&&space;1&space;\\&space;1&space;&&space;0&space;\end{matrix}&space;\right\}&space;\times&space;\dbinom{f(n-1)}{f(n-2)}=&space;\dbinom{f(n-1)&plus;f(n-2)}{f(n-1)}=&space;\dbinom{f(n)}{f(n-1)}" title="P2" /></a>
假设：
<a href="http://www.codecogs.com/eqnedit.php?latex=A=\begin{Bmatrix}&space;1&space;&&space;1&space;\\&space;1&space;&&space;0&space;\end{Bmatrix}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?A=\begin{Bmatrix}&space;1&space;&&space;1&space;\\&space;1&space;&&space;0&space;\end{Bmatrix}" title="P3" /></a>
则:
<a href="http://www.codecogs.com/eqnedit.php?latex=A^n\times&space;\dbinom{f(1)}{f(0)}=&space;\dbinom{f(n&plus;1)}{f(n)}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?A^n\times&space;\dbinom{f(1)}{f(0)}=&space;\dbinom{f(n&plus;1)}{f(n)}" title="P4" /></a>
A<sup>n</sup>可以通过矩阵快速幂直接求出，即可快速获得数列值。
### Fibonacci数列变种
如果现在要对Fibonacci数列的前N项求和，又该如何递推呢？
<a href="http://www.codecogs.com/eqnedit.php?latex=s(n)=f(1)&plus;f(2)&plus;...&plus;f(n)" target="_blank"><img src="http://latex.codecogs.com/gif.latex?s(n)=f(1)&plus;f(2)&plus;...&plus;f(n)" title="P5" /></a>
其实方法是一样的，关键在于找出递推矩阵，如下：
<a href="http://www.codecogs.com/eqnedit.php?latex=\begin{Bmatrix}&space;1&space;&&space;1&space;&0\\&space;0&space;&&space;1&1\\&space;0&1&0\\&space;\end{Bmatrix}&space;\times&space;\begin{Bmatrix}&space;s(n-1)\\&space;f(n)\\&space;f(n-1)\\&space;\end{Bmatrix}=&space;\begin{Bmatrix}&space;s(n-1)&plus;f(n)\\&space;f(n)&plus;f(n-1)\\&space;f(n)\\&space;\end{Bmatrix}=&space;\begin{Bmatrix}&space;s(n)\\&space;f(n&plus;1)\\&space;f(n)\\&space;\end{Bmatrix}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\begin{Bmatrix}&space;1&space;&&space;1&space;&0\\&space;0&space;&&space;1&1\\&space;0&1&0\\&space;\end{Bmatrix}&space;\times&space;\begin{Bmatrix}&space;s(n-1)\\&space;f(n)\\&space;f(n-1)\\&space;\end{Bmatrix}=&space;\begin{Bmatrix}&space;s(n-1)&plus;f(n)\\&space;f(n)&plus;f(n-1)\\&space;f(n)\\&space;\end{Bmatrix}=&space;\begin{Bmatrix}&space;s(n)\\&space;f(n&plus;1)\\&space;f(n)\\&space;\end{Bmatrix}" title="P6" /></a>
### 递推转矩阵递推
关键点在于如何找出递推矩阵，这主要通过数学方法，公式变形等。
将递推式左右两边先写入矩阵，然后构造A矩阵，不能直接推导的项就再补充几个项到矩阵中。
例1：
<a href="http://www.codecogs.com/eqnedit.php?latex=f(n)=f(n-1)&plus;f(n-3)" target="_blank"><img src="http://latex.codecogs.com/gif.latex?f(n)=f(n-1)&plus;f(n-3)" title="P7" /></a>
例1递推矩阵如下：
<a href="http://www.codecogs.com/eqnedit.php?latex=\begin{Bmatrix}&space;1&space;&&space;0&space;&1\\&space;1&space;&&space;0&0\\&space;0&1&0\\&space;\end{Bmatrix}&space;\times&space;\begin{Bmatrix}&space;f(n-1)\\&space;f(n-2)\\&space;f(n-3)\\&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n-1)&plus;f(n-3)\\&space;f(n-1)\\&space;f(n-2)\\&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n)\\&space;f(n-1)\\&space;f(n-2)\\&space;\end{Bmatrix}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\begin{Bmatrix}&space;1&space;&&space;0&space;&1\\&space;1&space;&&space;0&0\\&space;0&1&0\\&space;\end{Bmatrix}&space;\times&space;\begin{Bmatrix}&space;f(n-1)\\&space;f(n-2)\\&space;f(n-3)\\&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n-1)&plus;f(n-3)\\&space;f(n-1)\\&space;f(n-2)\\&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n)\\&space;f(n-1)\\&space;f(n-2)\\&space;\end{Bmatrix}" title="P8" /></a>
例2：
<a href="http://www.codecogs.com/eqnedit.php?latex=f(n)=f(n-1)&plus;n^2" target="_blank"><img src="http://latex.codecogs.com/gif.latex?f(n)=f(n-1)&plus;n^2" title="P8" /></a>
例2递推矩阵如下：
<a href="http://www.codecogs.com/eqnedit.php?latex=\begin{Bmatrix}&space;1&space;&&space;1&space;&0&0\\&space;0&space;&&space;1&2&1\\&space;0&0&1&1\\&space;0&0&0&1&space;\end{Bmatrix}&space;\times&space;\begin{Bmatrix}&space;f(n-1)\\&space;n^2\\&space;n\\&space;1&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n-1)&plus;n^2\\&space;n^2&plus;2n&plus;1\\&space;n&plus;1\\&space;1&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n)\\&space;(n&plus;1)^2\\&space;n&plus;1\\&space;1&space;\end{Bmatrix}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\begin{Bmatrix}&space;1&space;&&space;1&space;&0&0\\&space;0&space;&&space;1&2&1\\&space;0&0&1&1\\&space;0&0&0&1&space;\end{Bmatrix}&space;\times&space;\begin{Bmatrix}&space;f(n-1)\\&space;n^2\\&space;n\\&space;1&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n-1)&plus;n^2\\&space;n^2&plus;2n&plus;1\\&space;n&plus;1\\&space;1&space;\end{Bmatrix}&space;=&space;\begin{Bmatrix}&space;f(n)\\&space;(n&plus;1)^2\\&space;n&plus;1\\&space;1&space;\end{Bmatrix}" title="P9" /></a>
这里就不举更多的例子了，套路是一样的，可以自己随便写几个公式，然后按照上面的方法去推一下。


