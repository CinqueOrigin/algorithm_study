# ACWing92 递归实现指数型枚举

### 问题描述

问题链接：[92. 递归实现指数型枚举 - AcWing题库](https://www.acwing.com/problem/content/description/94/)

从 1∼n1∼n 这 nn 个整数中随机选取任意多个，输出所有可能的选择方案。

#### 输入格式

输入一个整数 nn。

#### 输出格式

每行输出一种方案。

同一行内的数必须升序排列，相邻两个数用恰好 11 个空格隔开。

对于没有选任何数的方案，输出空行。

本题有自定义校验器（SPJ），各行（不同方案）之间的顺序任意。

#### 数据范围

1≤n≤151≤n≤15

#### 输入样例：

```
3
```

#### 输出样例：

```
3
2
2 3
1
1 3
1 2
1 2 3
```

### 实现思路1
	本题的要求是输出所有的升序子序列，而本题给出的只是一个简单的数字，可以看作是一个从1到N的升序数组，枚举的方法很简单，本题可以采用递归也可以采用非递归的方法。
	递归的方法如下：一个函数exponent（i）的功能是求出从i到N的所有升序子序列，那么exponent(i)这个函数需要做如下几件事：
	首先递归的出口是i=N；然后递归调用exponent(i+1)，获得从i+1到N的所有升序子序列；
	接下来利用刚刚递归调用获得的子序列求出从i到N的所有升序子序列。
	比较可知，从i到N的子序列比i+1到N的子序列仅仅少了以i开头的所有升序子序列，而这些以i开头的升序子序列只需要在递归调用获得的所有子序列前加上i即可，从而就可以实现exponent(i)了。
	唯一需要注意的地方是我们采用的数据结构，每次实现升序子序列加i的操作时会加到数组的最后面，所以输出的时候要逆序输出，也可以修改该数据结构使得其可以在头部添加，具体的方法略。
	非递归方法：首先要有一个外层循环，类似于递归方法，每次循环求出从某个数到N的所有升序子序列，下一次循环求出这一个数的前一个数到N的所有升序子序列，直到这个数变为1。这里不给出代码，愿意的话可以自己尝试。

### 代码实现1

```java

package com.wuyun;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;
public class Main {
    static ArrayList<Integer> Num;
    static int N;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        N = Integer.parseInt(sc.next());
        ArrayList<ArrayList<Integer>> permutation=exponent(1);
        System.out.println("");
        for(ArrayList<Integer> c : permutation){
            Collections.reverse(c);
            for(int i :c){
                System.out.print(i+" ");
            }
            System.out.print("\n");
        }
    }

    /**
     * 第n个数之后的数
     * @param i
     */
    public static ArrayList<ArrayList<Integer>> exponent(int i){
        if(i == N) {
            ArrayList<ArrayList<Integer>> a = new ArrayList<ArrayList<Integer>>();
            ArrayList<Integer> b=new ArrayList<Integer>();
            b.add((N));
            a.add(b);
            return a;
        }
        ArrayList<ArrayList<Integer>> a=exponent(i+1);
        ArrayList<ArrayList<Integer>> b=new ArrayList<>();
        b.addAll(a);
        ArrayList<Integer> d=new ArrayList<Integer>();
        d.add(i);
        b.add(d);
        for(ArrayList<Integer> c : a){
            ArrayList<Integer> e = new ArrayList<>();
            e.addAll(c);
            e.add(i);
            b.add(e);
        }
        return b;
    }
}

```

### 实现思路2
	相信大家对求排列数的方法都很熟悉，本题也可以采用类似的dfs方法来实现，设计一个状态数组，大小至少为N，然后从1开始深度优先搜索。每次对i进行搜索，都先搜索i+1的节点，然后设置i为已经探索，然后继续搜索i+1的节点，最后进行回溯操作。递归的出口是数组访问到了N，此时进行一次输出。

### 代码实现2
```java
	    static boolean state[]=new boolean[16];
    public void DFS(int i){
        //递归出口
        if(i>N){
            for(int j=1;j<=N;j++){
                if(state[j]){
                    System.out.print(j+" ");
                }
            }
            System.out.println();
            return;
        }
        DFS(i+1);
        state[i]=true;//标记为这个位置为使用
        DFS(i+1);
        state[i]=false;//回溯操作
    }
    @Test
    public void DfsSolution(){
        Scanner sc = new Scanner(System.in);
//        N=sc.nextInt();
        N=3;
        DFS(1);
    }
```

### 实现思路3
	这个思路是从网上答案那里学习到的，用的是位运算，确实位运算相对来说是比较快的。
	大致思想是对于从0到$2^{N}$的所有二进制数i，每个i都对应着一个输出结果，如10010对应14,10011对应145；只要把这$2^{N}$的所有二进制数转化为对应的结果输出出来即可。

### 代码实现3
```java
	public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        for(int i = 0; i < 1 << n; i ++ ) {
            for(int j = 0; j < n; j ++ ) {
                int t = i >> j;
                if((t & 1) == 1) 
                    System.out.print(j + 1 + " ");
            }
            System.out.println();
        }
    }
    //链接：https://www.acwing.com/solution/content/49483/
```