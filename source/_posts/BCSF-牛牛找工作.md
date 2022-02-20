---
title: 牛牛找工作
date: 2022-01-07 14:22:35
tags: [C++]
categories: 编程算法
widgets: null
---

链接：https://www.nowcoder.com/questionTerminal/e3b606c7128249fd8d3a92bf2d46817f
来源：牛客网

为了找到自己满意的工作，牛牛收集了每种工作的难度和报酬。牛牛选工作的标准是在难度不超过自身能力值的情况下，牛牛选择报酬最高的工作。在牛牛选定了自己的工作后，牛牛的小伙伴们来找牛牛帮忙选工作，牛牛依然使用自己的标准来帮助小伙伴们。牛牛的小伙伴太多了，于是他只好把这个任务交给了你。



##### **输入描述:**

```
每个输入包含一个测试用例。
每个测试用例的第一行包含两个正整数，分别表示工作的数量N(N<=100000)和小伙伴的数量M(M<=100000)。
接下来的N行每行包含两个正整数，分别表示该项工作的难度Di(Di<=1000000000)和报酬Pi(Pi<=1000000000)。
接下来的一行包含M个正整数，分别表示M个小伙伴的能力值Ai(Ai<=1000000000)。
保证不存在两项工作的报酬相同。
```





##### **输出描述:**

```
对于每个小伙伴，在单独的一行输出一个正整数表示他能得到的最高报酬。一个工作可以被多个人选择。
```

示例1

## 输入

```
3 3 
1 100 
10 1000 
1000000000 1001 
9 10 1000000000
```

## 输出

```
100 
1000 
1001
```

<!--more-->

首先，对于相同难度的工作，仅保留报酬高的，因为这两种工作无论能不能做都会选择报酬高的 

其次，抛弃报酬低，要求还高的工作，因为这种情况下会选择报酬高能力还要求不高的 

最后，再根据自己的能力来选择能够最大限度发挥自己才能的工作

```java
import java.util.Scanner;
import java.util.TreeMap;
import java.util.Map.Entry;

public class Main {
    public static void main(String
[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt(), m = sc.nextInt();
        // 存储工作难度及其报酬
        TreeMap<Integer, Integer> map1 = new TreeMap<>();
        for(int i = 0; i < n; i++){
            int d = sc.nextInt(), p = sc.nextInt();
            // 如果工作难度相同，只记录报酬多的
            if(map1.containsKey(d)){
                if(p > map1.get(d)) map1.put(d, p);
            }else
                map1.put(d, p);
        }
        // TreeMap是按key排好序的，因此按顺序遍历key，抛弃能力要求高且报酬比能力要求低的工作还少的工作
        TreeMap<Integer, Integer> map2 = new TreeMap<>();
        Entry<Integer, Integer> preEntry = map1.firstEntry();
        map2.put(preEntry.getKey(), preEntry.getValue());
        for(Entry<Integer, Integer> e: map1.entrySet()){
            if(e.getValue() > preEntry.getValue()){
                map2.put(e.getKey(), e.getValue());
                preEntry = e;
            }
        }
        for(int i = 0; i < m; i++){
            // 获得刚好低于等于自己工作能力的entry
            Entry<Integer, Integer> floorEntry = map2.floorEntry(sc.nextInt());
            int pout = 0;
            // 获得这个工作的报酬并输出
            if(floorEntry != null) pout = floorEntry.getValue();
            System.out.println(pout);
        }
    }
}
```



```C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
class Data{
public:
    int hard;
    int val;
public:
    Data(int hard = 0, int val = 0):hard(hard), val(val){}
};
class comp{
public:
    bool operator()(const Data &d1, const Data &d2)const{
        return d1.hard < d2.hard;
    }
};
int main(){
    //先按照工作的难度排序
    int n, m;
    cin>>n>>m;
    vector<Data> v(n);
    for (int i = 0; i < n; ++i){
        Data d;
        cin>>d.hard>>d.val;
        v
[i] = d;
    }
    sort(v.begin(), v.end(), comp());
    //预处理每个位置的最大值
    vector<int> maxs(n);
    maxs
[0] = v
[0].val;
    for (int i = 1; i < n; ++i){
        maxs
[i] = max(v
[i].val, maxs
[i-1]);
    }
    for (int i = 0; i < m; ++i){
        int able;
        cin>>able;
        //二分查找小于等于他能力值的最右位置
        int l = 0, r = n - 1;
        int index = -1;
        while(l <= r){
            int m = l + ((r - l) >> 1);
            if (v
[m].hard > able){
                r = m - 1;
            }else{
                index = m;
                l = m + 1;
            }
        }
        cout<<maxs
[index]<<endl;
    }
    return 0;
}
```

