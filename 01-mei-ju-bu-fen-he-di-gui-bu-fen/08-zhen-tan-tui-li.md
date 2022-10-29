# ACWing 478侦探推理

### 题目描述

问题链接：[478. 侦探推理 - AcWing题库](https://www.acwing.com/problem/content/480/)

明明同学最近迷上了侦探漫画《柯南》并沉醉于推理游戏之中，于是他召集了一群同学玩推理游戏。

游戏的内容是这样的，明明的同学们先商量好由其中的一个人充当罪犯（在明明不知情的情况下），明明的任务就是找出这个罪犯。

接着，明明逐个询问每一个同学，被询问者可能会说：

| 证词内容           | 证词含义                                                     |
| :----------------- | :----------------------------------------------------------- |
| I am guilty.       | 我是罪犯                                                     |
| I am not guilty.   | 我不是罪犯                                                   |
| XXX is guilty.     | XXX是罪犯（XXX表示某个同学的名字）                           |
| XXX is not guilty. | XXX不是罪犯                                                  |
| Today is XXX.      | 今天是XXX（XXX表示星期几，是Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday中的一个） |

证词中出现的其他话，都不列入逻辑推理的内容。

明明所知道的是，他的同学中有 NN 个人始终说假话，其余的人始终说真。

现在，明明需要你帮助他从他同学的话中推断出谁是真正的凶手，请记住，凶手只有一个！

#### 输入格式

输入由若干行组成，第一行有三个整数，M、NM、N 和 PP；MM 是参加游戏的明明的同学数，NN 是其中始终说谎的人数，PP 是证言的总数。

接下来 MM 行，每行是明明的一个同学的名字（英文字母组成，没有空格，全部大写）。

往后有 PP 行，每行开始是某个同学的名字，紧跟着一个冒号和一个空格，后面是一句证词，符合前表中所列格式。

证词每行不会超过 250250 个字符。

输入中不会出现连续的两个空格，而且每行开头和结尾也没有空格。

#### 输出格式

如果你的程序能确定谁是罪犯，则输出他的名字；如果程序判断出不止一个人可能是罪犯，则输出 `Cannot Determine`；如果程序判断出没有人可能成为罪犯，则输出 `Impossible`。

#### 数据范围

1≤M≤201≤M≤20,
1≤N≤M1≤N≤M,
1≤P≤1001≤P≤100

#### 输入样例：

```
3 1 5
MIKE
CHARLES
KATE
MIKE: I am guilty.
MIKE: Today is Sunday.
CHARLES: MIKE is guilty.
KATE: I am guilty.
KATE: How are you??
```

#### 输出样例：

```
MIKE
```

### 算法思路

起初我想的是枚举说谎的N个人，但是这样的话就会有$2^{n}$种情况，时间复杂度有点过于大了。后来想了想，干脆枚举这M个人谁是凶手就好了，然后循环一周里的每一天，因为只有一个凶手，所以每句证词都能够判断出是正确的还是错误的，理想情况下能直接求出说谎的人数，从而直接判断是否等于N即可。但是实际情况下可能会出现有的人不说话，甚至有的人既说真话又说假话，那么就要考虑到这些特殊情况了，如果既说真话又说假话，那么可以直接跳过这一次判断，进行下一次判断即可；如果有的人没说话或者说了废话，判断不了说的是真还是假，那么我们最终能够判断出说谎的人数、说真话的人数、判断不了的人数；这种情况下，如果说谎的人数小于等于N并且说谎的人数加上判断不了的人数大于等于N，那么这种情况也应该算作在内。如果最后发现可能有不止一个人是罪犯，那么就输出Cannot Determine；如果程序结束还没有人成为罪犯，输出Impossible。理论上这种情况下的时间复杂度是O(M * 7 * P)人，但是每句证词的发言人不能直接获得，需要遍历一遍存放名字的数组，这种情况下是$O(7M^{2}P)$。这里比较考验代码结构的设计，应当使用模块化设计方法，但是我这里从一开始想偷懒懒得写函数，最后代码的结构非常繁冗，建议大家写复杂代码的时候使用模块化设计，下面这个yxc写的题解就非常好：[AcWing 478. 侦探推理 - AcWing](https://www.acwing.com/solution/content/2811/)。

另外本题还需要能够熟练运用字符串的处理方法。而且本题拥有大量输入数据，对于java语言来说应该使用一种更快速的获取输入的方法，我这里使用BufferedReader。

了解了思路之后本题看起来很简单，但是实际操作起来却总会遇到问题，也就是大家常说的“这题很麻烦”，还希望大家能够静下心来解决遇到的问题。

### 字符串处理简介

在本题中需要处理字符串，这里就来简单介绍几种常见的字符串处理方法，如果熟悉这部分的话可以略过这个环节直接看下面的代码。

#### 1.基本操作函数

```java
int length = str.length();	//获取字符串长度
char c = str.charAt(i);		//获取以i为索引处的字符
char[] characters = new char[N];
str.getChars(begin,end,characters,copybegin);//begin、end是被复制的字符串的起始；characters复制到的数组的数组名称;copybegin是指从characters哪个位置开始粘贴。

```

#### 2.字符串比较

我们查字典时拼音chen是在cheng之前，这两个读音也在chun之前。

而字符串的比较和上面的字典序是类似的，只是每个字符之间的比较是按照字符集来进行的，字符集通常我们用ASCII和Unicode，一般的问题里ASCII比较多。

```java
int result = str1.compareTo(str2);	//这种情况下不忽略大小写，比如大写A是比小写a要小的。这种情况是若str1的字典序小于str2，返回-1；等于返回0；大于返回1；
int result = str1.compareToIgnoreCase(str2);//这种情况下忽略大小写，比如大写A是与小写a相等的。这种情况是若str1的字典序小于str2，返回-1；等于返回0；大于返回1；
boolean result = str1.equals(str2);//这种情况下不忽略大小写，比如大写A是比小写a要小的。这种情况是若str1的字典序等于str返回1；否则返回0；
boolean result = str1.equals(str2);//这种情况下忽略大小写，比如大写A是比与小写a相等的。这种情况是若str1的字典序等于str返回1；否则返回0；
```

#### 3.字符串输出

我们可以把一个数组转化为字符串，然后以字符串的形式输出，数组可以是byte类型也可以是char类型。

```java
byte[] b={1,2,333};	//char类型也可以
String str=new String(b);
//String str=new String(b,start,length); //这是把start开始的length长度的元素输出
System.out.println(str);
```

#### 4.类型转换

```java
String strb1 = String.valueOf(变量); //这里的变量可以是int/double/float/char/byte/boolean/long等等
```

#### 5.字符串查找

首先是查找特定字符的出现位置

```java
//这里的character可以是字符类型，也可以是字符串类型，如果是字符串的话返回该字符串第一个元素的索引号
int index = str.indexOf(character);	//查找character在str字符串中第一次出现的位置的索引，如果没有找到，就返回-1；
index = str.indexOf(character,fromIndex); //查找character在str字符串中在formIndex之后的第一次出现的位置的索引，如果没有找到，就返回-1；
index = str.lastIndexOf(character);		//查找指定字符在字符串中最后一次出现位置的索引，没有返回-1
index = str.lastIndexOf(character,fromIndex);	//查找指定索引位置之前最后一次出现该字符的索引号
```

#### 6.字符串截取

```java
String result = str.substring(begin,end)；//截取子字符串，从begin到end；但是要注意这里实际上获取的字符串是[begin,end),举个例子substring(3,4)只能截取出一个字符，它的索引号是3。
String strs[] = str.split(正则表达式);		//按照正则表达式的内容分割字符串
String strs1[] = str.split(" ");		//例如会把"hello    world hei hei"分割成四个字符串，hello,world,hei,hei；
String strs2[] = str.split（" {2，}");	//这个的分割符是正则表达式“ {2，}”，意思是两个或两个以上的空格。"hello    world hei hei"会被分割成hello,world hei hei两个字符串。

```

#### 7.替换和修改

```java
String result = str1.concat(str2);   //将str1和str2合并,保存到result中
String result2 = str.toLowerCase();		//将str中所有字母转化为小写，保存到result2中
String result3 = str.toUpperCase();      //将str中所有字母转化为大写，保存到result2中
String result4 = str.replaceFirst(正则表达式,替换字符串)；//按正则表达式在字符串中查找，把找到的第一个元素修改为替换字符串。
String result4 = str.replaceAll(正则表达式,替换字符串)；//按正则表达式在字符串中查找，把找到的所有元素修改为替换字符串。
```



### 实现代码

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Scanner;
public class Main {
	static int M,N,P;	//players,liers,testimonies
	static String names[];	//players names
	static String testimoneies[];
	static String speakers[];
	static String sentences[];
	static Map<String, Integer> map;	//true:lier false:honest
	static BufferedReader in;
	static String days[] = new String[] {"Today is Monday.","Today is Tuesday.","Today is Wednesday.","Today is Thursday.","Today is Friday.","Today is Saturday.","Today is Sunday."};
	public static void main(String args[]) throws IOException{
		in = new BufferedReader(new InputStreamReader(System.in));
		Scanner sc = new Scanner(System.in);
		M=sc.nextInt();
		N=sc.nextInt();
		P=sc.nextInt();
//		System.out.println(M+N+P);
		names = new String[M];
		testimoneies = new String[P];
		speakers = new String[P];
		sentences = new String[P];
		map = new HashMap<>();
		String name;
		String tempString="";
		//名字数组
		tempString=sc.nextLine();
		for(int i =0;i<M;i++) {
			name=sc.nextLine();
//			System.out.println(name);
			names[i]=name;
			map.put(name, -1);
		}
//		System.out.println("start test");
		//证词处理
		for(int i =0;i<P;i++) {
			testimoneies[i]=sc.nextLine();
//			System.out.println(testimoneies[i]);
			speakers[i]=testimoneies[i].split(": ",2)[0];
//			System.out.println(speakers[i]);
			sentences[i]=testimoneies[i].split(": ",2)[1];
//			System.out.println(sentences[i]);
		}
		int count=0,num=0,count1=0;
		int state=-1;
		boolean flag=true;
		String speaker,name1,criminal,sentence;	//存储证词中提取出来的发言人，日期，罪犯的名字；以及枚举的罪犯的名字
		//循环假设这M个人是罪犯
		for(Map.Entry<String, Integer> entry : map.entrySet()) {
			//获取假设的罪犯的名字
			criminal=entry.getKey();
//			System.out.println(criminal);
			//循环假设一周七天哪一天是今天
			for(int j=0;j<7;j++) {
				count=0;
				count1=0;
				flag=true;
				//1.复制从输入中获取的map
				Map<String, Integer> copyMap = new HashMap<>();	
				copyMap.putAll(map);
//				System.out.println(copyMap);
				//2.循环判断P句证词
				day:
				for(int k=0;k<P;k++) {
//					System.out.println(testimoneies[k]);
					//1.获取是谁在说话
					speaker = speakers[k];
					sentence = sentences[k];
//					System.out.println(sentence);
					//2.说的话是关于日期的
					for(int m =0;m<7;m++) {
						if(sentence.equals(days[m])) {
							state=(m!=j)?1:0;
						if(state+copyMap.get(speaker) != 1)
							copyMap.put(speaker, state);
						else {flag=false;break day;}
						}
					}
					//3.说的话关于xxx是/不是罪犯
					if(sentence.toLowerCase().equals("i am guilty.") ) {
						state=(!speaker.equals(criminal))?1:0;
						if(state+copyMap.get(speaker) != 1)
						copyMap.put(speaker, state);
						else {flag=false;;break;}
						}
					else if (sentence.toLowerCase().equals("i am not guilty.")	) {
						state=(speaker.equals(criminal))?1:0;
						if(state+copyMap.get(speaker) != 1)
						copyMap.put(speaker, state);
						else {flag=false;;break;}
					}
					else if(sentence.contains(" is ")&&sentence.split(" is ",2)[1].equals( "guilty.")) {
						name1=sentence.split(" is ",2)[0];
						if(copyMap.get(name1) !=null)
							{
							state=(!name1.equals( criminal))?1:0;
							if(state + copyMap.get(speaker) !=1)
								copyMap.put(speaker, state);
							else {flag=false;;break;}
							}
						else break;
					}
					else if(sentence.contains(" is ")&&sentence.split(" is ",2)[1].equals( "not guilty.")) {
						name1=sentence.split(" is ",2)[0];
						if(copyMap.get(name1) !=null)
							state=(name1.equals( criminal))?1:0;
							if(state + copyMap.get(speaker) !=1)
								copyMap.put(speaker, state);
							else {flag=false;;break;}
					}
					//4.说的是废话
				}
				if(flag) {
				Iterator<Map.Entry<String, Integer>> it =copyMap.entrySet().iterator();	//遍历名字
				while(it.hasNext()) {
					Map.Entry<String, Integer> en =it.next();
					if(en.getValue() == 1) count++;
					else if(en.getValue() == -1) count1++;
				}
//				System.out.println(copyMap);
				if(count<=N&&count+count1>=N&&(tempString == ""||!tempString.equals(criminal))) {num++;tempString=criminal;}
				if(num>1) break;
				}
			}
		}
		if(num == 0) System.out.println("Impossible");
		else if (num == 1) System.out.println(tempString);
		else System.out.println("Cannot Determine");
	}
}

```

