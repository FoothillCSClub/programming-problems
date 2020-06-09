---
layout: page
---

# Problem 5: Servicing Stations

A company offers personal computers for sale in N towns (3 ≤ N ≤ 35), denoted
by 1, 2, . . . , N . There are direct routes connecting M pairs among these towns. The
company decides to build servicing stations to ensure that for any town X, there will
be a station located either in X or in some immediately neighboring town of X.

Write a program to find the minimum number of stations the company has to build.

**Input**

The input consists of multiple problem descriptions. Every description starts with num-
ber of towns N and number of town-pairs M , separated by a space. Each of the next
M lines contains a pair of integers representing connected towns, at one pair per line
with each pair separated by a space. The input ends with N = 0 and M = 0.

**Output**

For each input case, print a line reporting the minimum number of servicing stations
needed.

**Sample Input**
```
8 12
1 2
1 6
1 8
2 3
2 6
3 4
3 5
4 5
4 7
5 6
6 7
6 8
0 0
```

**Sample Output**
```
2
```
