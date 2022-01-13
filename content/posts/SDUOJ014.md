---
title: SDUOJ 划分石头题解二 (二分查找)
date: 2021-04-16 15:13:06
tags: [算法]
categories: [算法,SDUOJ]
summary: 今天，你AC了吗？


---



## 题目描述

海边有很多好看的石头🪨，war把它们收集起来依次排成一排，一共n个，第i个石头的重量为w_i*w**i*。他想将其分成m段，每一段连续，并且**重量和**最大的段的**和最小**。请你帮帮他～

## 输入描述

第一行为n(1≤n≤10^5)(1≤*n*≤105)，m(m≤n)(*m*≤*n*)。

第二行n个数，第i个数为w_i(0≤wi≤10^9)*w**i*(0≤*w**i*≤109)，中间有空格。

## 输出描述

输出只有一个数，**重量和**最大的段的和。



## 数据结构

#### 算法数据

| 标识符            | 解释               |
| ----------------- | ------------------ |
| stones[0..100010] | 存储石头的重量     |
| maxMass           | 界定二分查找的下界 |
| inputSum          | 界定二分查找的上界 |



## 算法设计

#### 二分答案：最小化最大值

根据输入，确定上述上下界，开始二分查找。

判断函数的关键，在于输入一个判断值d作为每段的质量，判断尽量`以此为最小质量`，能否分得`<=m`段。因为如果d过小，显然分段数量要很大。如果d足够大了，分段数量就可以<=m，那么显然`能满足`“可以分到m段”的条件。

具体实现方式在于，按顺序遍历所有石头，并计算当前段的质量和。如果当前质量和+下一石头质量超过了d，则分段数++，其余量归零。



## 关键问题

可以通过输入来确定二分答案的上下界，加快速度。



## 代码实现

```c++
#include <bits/stdc++.h>

using namespace std;
const int INF = 1e9;

unordered_map<int, int> stones;
int n, m;
int maxMass, inputSum;

bool C(int d) {
	int massSum = 0, divides = 0;

	for (int i = 1; i <= n; i++) {
		if (massSum + stones[i] > d) {
			divides++;
			massSum = stones[i];
		}
		else massSum += stones[i];
	}
	if (divides >= m)return false;
	return true;
}

void solve() {
	int lb = maxMass, ub = inputSum, mid = 0, ans = 0;
	while (lb<=ub)
	{
		mid = (ub + lb) / 2;
		if (C(mid)) {
			ans = mid;
			ub = mid - 1;
		}
		else lb = mid + 1;
	}
	cout << ans << endl;
}

int main() {
	//freopen("C:\\Users\\Lenovo\\Desktop\\a.in","r",stdin);
	//freopen("C:\\Users\\Lenovo\\Desktop\\a.out", "w", stdout);
	cin >> n >> m;
	for (int i = 1; i <= n; i++) {
		cin >> stones[i]; 
		if (stones[i] > maxMass)maxMass = stones[i];
		inputSum += stones[i];
	}
	solve();
}

```

