---
title: Edit_Distance
date: 2021-03-27 19:32:07
tags:
- dp
- algorithm
categories:
- algorithm
- dp
---

Given two strings `word1` and `word2`, return *the minimum number of operations required to convert `word1` to `word2`*.

You have the following three operations permitted on a word:

- Insert a character
- Delete a character
- Replace a character

**Example 1:**

```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

**Example 2:**

```
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

 

**Constraints:**

- `0 <= word1.length, word2.length <= 500`
- `word1` and `word2` consist of lowercase English letters.

**Solution:**

```c++
class Solution {
public:
    int minDistance(string s1, string s2) {
     int m=s1.size();
     int n=s2.size();
     int t[m+1][n+1];
     for(int i=0;i<m+1;i++)
       t[i][0]=i;
     for(int i=0;i<n+1;i++)
       t[0][i]=i;
     for(int i=1;i<m+1;i++){
        for(int j=1;j<n+1;j++){
           if(s1[i-1]==s2[j-1])
              t[i][j]=t[i-1][j-1];
           else
             t[i][j]=1+min(t[i-1][j], min(t[i][j-1], t[i-1][j-1])); //remove, insert, replace
        }
     } 
     return t[m][n];
   }
};
```

#### 理解：

画图：

当前状态如下：

![image-20210327194323030](/images/image-20210327194323030.png)

如果两字符串$s1[i]=s2[j]$：相当于什么也不用做，所有操作都在前面

#### remove：

这里因为$s1[i]\neq s2[j]$:

![image-20210327195102599](/images/image-20210327195102599.png)

#### insert：

![image-20210327194943592](/images/image-20210327194943592.png)

#### replace：

![image-20210327194815200](/images/image-20210327194815200.png)

动态规划的方程：$f(i,j) = \begin{cases} 1+min(f(i-1,j),\ f(i,j-1) \ f(i-1,j-1)) & str1[1] \neq str2[j] \\ f(i-1,j-1) & \text{str1[i] = str2[j]} \end{cases}$

