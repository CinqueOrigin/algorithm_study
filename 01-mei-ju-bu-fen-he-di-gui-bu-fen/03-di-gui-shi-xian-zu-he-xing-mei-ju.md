# ACWing1573 递归实现组合型枚举 Ⅱ

### 问题描述

给定一个长度为 nn 的可包含重复数字的序列，从中随机选取 mm 个数字，输出所有可能的选择方案。

题目链接：[1573. 递归实现组合型枚举 II - AcWing题库](https://www.acwing.com/problem/content/1575/)

#### 输入格式

第一行包含两个整数 n,mn,m。

第二行包含 nn 个正整数。

#### 输出格式

按照从小到大的顺序输出所有方案，每行 11 个。

首先，同一行内的数升序排列，相邻两个数用一个空格隔开。

其次，对于两个不同的行，对应下标的数一一比较，字典序较小的排在前面（例如1 3 5 7排在1 3 6 8前面）。

#### 数据范围

n>0n>0,
0≤m≤n0≤m≤n,
n+(n−m)≤25n+(n−m)≤25,
序列内所有元素均不大于 nn。

#### 输入样例：

```
5 3
1 2 2 3 3
```

#### 输出样例：

```
1 2 2
1 2 3
1 3 3
2 2 3
2 3 3
```

### 算法思路
	先考虑不给重复数字的组合问题。
排列组合似乎一直都是成对的，所以这道题会下意识地从递归实现排列枚举往过推导。但实际上这道题和DFS递归实现指数型枚举更像一点，因为每个位置上只有两种可能：出现/不出现，只不过在那道题指数型的题中不需要保证长度，而这道题需要保证长度是刚刚输入的M。想清楚这一点，这题就很好解了。
	然后考虑给了重复数字的组合问题。
给了重复数字的话，同样要避免交换的情况，我们同样可以先给这个输入的数组排个序，然后由于它的输出一定是字典序的，我们可以用一个另外的数组记录上一次的输出结果，如果本次输出的结果和上一次一致的话那么跳过这次输出。如果想用排列那道题那样的循环扫描跳过的方式的话，就要增加一个数据结构来记录当前已经用了哪些数字了，实际上就是递归排列中的Temp数组。那么这次应该如何跳过重复的呢？排列的重复只要防止交换就好，那组合的呢？注意到重复的组合数往往诞生于（排序后）相邻的数，从前一个数使用后一个数不使用到前一个数不使用后一个数使用的状态转换的过程，那么只要跳过这个过程就可以了。值得注意的是，这个跳过的过程的优先级要高于进行输出的递归出口，而且输出的递归出口的优先级又要高于数组越界的递归出口。
	但是同样，输出还是太慢了，最后居然比c++慢了30倍，我也是服了，怪不得大家都用c++。
### 实现代码
```java
package com.wuyun;

import javax.swing.plaf.nimbus.State;
import java.io.*;
import java.util.Arrays;

public class EnumCombination {
    static int N,M;
    static int[] Num,Temp;
    static boolean[] state;
    static int count;
    static PrintWriter out = new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
    public static void DFS(int i){
        if(i<N&&i>0&&Num[i-1]==Num[i]&&!state[i-1]){
            DFS(i+1);
            return;
        }
        else if(count == M){
            for(int j =0;j<M;j++){
                out.print(Temp[j]+" ");
            }
            out.println();
        }
        else if(i == N) return;
        else {
            //必须先设置这一步，不然不是字典序
            Temp[count]=Num[i];
            state[i]=true;
            count++;
            DFS(i+1);
            count--;    //回溯操作
            state[i]=false;
            DFS(i+1);
        }
    }
    public static void main(String[] args) throws IOException {
        StreamTokenizer in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
        in.nextToken();
        N=(int)in.nval;
        in.nextToken();
        M=(int)in.nval;
        Num=new int[N];
        Temp=new int[M];
        state=new boolean[N];
        for(int j =0;j<N;j++){
            in.nextToken();
            Num[j]=(int)in.nval;
        }
        Arrays.sort(Num);
        count=0;
//        long Time1 = System.nanoTime();
        DFS(0);
//        long Time2 = System.nanoTime();
//        out.println("time:"+(Time2-Time1)/1000000);
        out.flush();
    }
}

```
