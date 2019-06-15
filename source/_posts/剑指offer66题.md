---
title: 剑指offer66题
date: 2019-06-01 17:45:04
tags: [剑指offer,C++]
categories: [剑指offer]
---

# 20-包含min函数的栈

## 题目描述

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））

---


## 解题思路

我们用一个辅助栈来实现最小值的更新工作。

这个辅助栈工作原理：
- 入栈时：
    - 1）当数据栈为空时，进入栈的元素同时也进入辅助栈；
    - 2）当它不为空时，就把该入栈元素与辅助栈的栈顶元素进行比较，若入栈元素小，则该元素同时也进入辅助栈；若不是，则对辅助栈不进行操作

- 出栈时：
    - 1）当时辅助栈的栈顶元素等于处理数据的数据栈栈顶元素时，不经数据栈要弹出元素，辅助栈也要弹出栈顶元素，
    - 2)当不等时，只对数据栈进行出栈操作。

这样我们思路就很明确了：**min函数只需返回辅助栈的栈顶源。**
```C++
class Solution {
public:
    void push(int value)
    {
        datastack.push(value);
        //辅助栈为空或者即将入栈的值小于辅助栈栈顶元素
        if(minstack.empty() || value < minstack.top())
            minstack.push(value);
    }

    void pop()
    {
        if(datastack.empty())//数据栈为空
            return ;
        if(datastack.top() == minstack.top()) //数据栈和辅助栈栈顶元素相同
            minstack.pop();
        datastack.pop();
    }

    int top()
    {
        return datastack.top();
    }

    int min()
    {
        return minstack.top();
    }
private:
    stack<int>  datastack;     //  数据栈
    stack<int>  minstack;      //  存储每次栈中最小值的栈信息
};
```
---

# 21-栈的压入弹出序列


## 题目描述

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

---


## 解题思路

开辟一个辅助栈，模拟入栈出栈过程(假设pushV为入栈序列，popV为出栈序列)

pushV中的元素依次压入辅助栈s,push++；设置变量push,pop分别代表pushV和popV当前元素的位置；

新压入的元素与弹出序列的pop位元素相同，辅助栈弹出，同时pop++

不相同，pushV中的元素继续入辅助栈s，push++；

- 如果下一个弹出的数字刚好是栈顶数字，则直接弹出。

- 若下一个弹出的数字不在栈顶，则把压栈序列中还没有入栈的数字压入辅助栈，直到把下一个需要弹出的数字压入栈顶为止。

- 若所有的数字都压入栈了仍没有找到下一个弹出的数字，则表明该序列不可能滴一个弹出序列。
```C++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if(pushV.size() == 0 && popV.size() == 0)
            return false;
        else if(pushV.size() != popV.size())
            return false;
        stack<int> s;
        s.push(pushV[0]);
        for(int push = 0, pop = 0; push < pushV.size() && pop < popV.size();){
            if(s.empty() != true && s.top() == popV[pop]){
                s.pop();
                pop++;
            }
            else{
                s.push(pushV[++push]);
            }
        }
        if(s.empty())
            return true;
        else
            return false;
    }
};
```
---

# 22-从上往下打印二叉树


## 题目描述

从上往下打印出二叉树的每个节点，同层节点从左至右打印

---


## 解题思路

在队列中插入结束标识来标识当前层结束

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/

#include<queue>

class Solution {
public:
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> res;
        if(!root) return res;
        queue<TreeNode* > q;
        q.push(root);
        q.push(NULL);// 在队列中插入结束标识来表示当前层结束
        while(!q.empty()){
            TreeNode* node = q.front();
            q.pop();
            if(node){
                res.push_back(node->val);
                if(node->left != NULL)
                    q.push(node->left);
                if(node->right != NULL)
                    q.push(node->right);
            }
            else if(!q.empty())
                q.push(NULL);
        }
        return res;
    }
};
```
---

# 23-二叉搜索树的后序遍历序列

## 题目描述

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

---


## 解题思路

利用递归，后序遍历中最后一位是根节点，然后将序列前面分成两部分，前面部分比根节点小的为左子树，中间部分比根节点大的为右子树；要考虑最后一层的孩子节点为单孩子还是双孩子节点

```C++
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.size() == 0){
            return false;
        }
        return judge(sequence, 0, sequence.size() - 1);
    }
    bool judge(vector<int> &sequence, int left, int right){
        if(left >= right){ //考虑有左右孩子和单孩子的请况
            return true;
        }
        int mid = right - 1;
        while(sequence[mid] > sequence[right]){
            mid--;
        }
        int i = left;
        while (i < mid && sequence[i] < sequence[right]){
            i++;
        }
        if (i < mid){
            return false;
        }
        ///  这样我们就划分出区间
        ///  [left, mid] 是左子树
        ///  [mid + 1, right - 1] 是右子树
        ///  right 是根节点
        return judge(sequence, left, mid) && judge(sequence, mid + 1, right - 1);
    }
};
```
---
# 24-二叉树和为某一值的路径


## 题目描述

输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

---


## 解题思路

用个递归来实现，先序遍历;
- 每次访问一个节点，那么就将当前权值求和
- 如果当前权值和与期待的和一致，那么说明我们找到了一个路径，保存或者输出
- 否则的话就递归其左右孩子节点 

> 这里需要注意一个问题，就是递归退出的时候，权值和的信息是保存在递归栈中的会恢复，但是我们保存的路径是无法恢复的，那么我们就需要在递归返回时将数据弹出。


```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<vector<int> > ans;
    vector<vector<int> > FindPath(TreeNode* root,int target){
        if(root == NULL)
            return ans;
        vector<int> path;
        FindToPath(root, target, path, 0);
        return ans;
    }
    void FindToPath(TreeNode* root, int target, vector<int> path, int currentsum){
        currentsum += root->val;
        path.push_back(root->val);
        if(target == currentsum && root->left == NULL && root->right == NULL)
            ans.push_back(path);
        if(root->left != NULL)
            FindToPath(root->left, target, path, currentsum);
        if(root->right != NULL)
            FindToPath(root->right, target, path, currentsum);
        //  此处不需要恢复currentSum和path的值:                                  
        //  因为currentSum作为参数在函数递归调用返回时会自动恢复                 
        //  而如果作为静态局部变量存储则需要进行恢复                             
        //  currentSum -= root->val;                                               
        //  path.pop_back( );
    }
};
```
---
# 28-数组中出现次数超过一半的数字


## 题目描述

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

---


## 解题思路

**充分利用出现次数超过一半这个条件**

数组中有一个数字出现的次数超过数组长度的一半，也就是说它出现的次数比其他所有数字出现的次数之和还要多

我们考虑**阵地攻守**（镇守阵地），遇见一个友军就抱成团，遇见一个敌军就同归于尽，那么最后战场上剩余的肯定就是人数（出现次数）最多的那个队伍（数字）

采用阵地攻守的思想：

- 第一个数字作为第一个士兵，守阵地；count = 1；

- 遇到相同元素，count++; 遇到不相同元素，即为敌人，同归于尽,count--；

- 当遇到count为0的情况，又以新的i值作为守阵地的士兵，继续下去，到最后还留在阵地上的士兵，有可能是主元素。

- 再加一次循环，记录这个士兵的个数看是否大于数组一般即可。

> 由于我们要找的数字出现的次数比他所有数字出现的次数之和还要多，那么要找的数字肯定是最后一次把次数设为1时对应的数字，因为少的元素都已经阵亡了，战场上仅剩下的一定是人数数目要多的那个队伍

---

```C++
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        if(numbers.size() == 0)
            return 0;
        else if(numbers.size() == 1)
            return numbers[0];
        int len = numbers.size(), key = numbers[0], count = 1;
        for(int i = 1; i < len; i++){
            if(key == numbers[i]){
                count++;
            }
            else{
                count--;
                if(count < 1){
                    key = numbers[i];
                }
            }
        }
        count = 0;
        for(int j = 0; j < len; j++){
            if(key == numbers[j])
                count++;
        }
        if(count > (len / 2))
            return key;
        return 0;
    }
};
```
---
# 29-最小的K个数


## 题目描述

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

---


## 解题思路

采用冒泡排序法, K趟找出前K个数字

---

```C++
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
    vector<int> res;
    int len = input.size();
    if(len < k)
        return res;
    
    for(int i = 0; i < k; i++){ //冒泡排序前K个元素
        for(int j = len - 1; j > 0; j--){
            if(input[j] < input[j - 1])
                swap(input[j], input[j - 1]);
        }
        res.push_back(input[i]);
    }
    return res;
}
```
---
# 30-连续子数组的最大和
## 题目描述

HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)

---


## 解题思路

利用**贪心思想**
如果希望达到O(n)时间复杂度，我们就应该能够想到我们只能对整个数组进行一次扫描，在扫描过程中求出最大连续子序列和以及子序列的起点和终点位置。

这个方法其实就是动态规划算法的改进

- 如果当前和为负数，那么就放弃前面的累加和，从数组中的下一个数再开始计数

- 否则我们就继续累计，并且保存当前的累计和
---

``` c++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if(array.size() == 0)
            return 0;
        int sum = 0, maxsum = INT_MIN;
        for(int i = 0; i < array.size(); i++){
            sum += array[i];
            sum = max(sum, array[i]);
            maxsum = max(sum, maxsum);
        }
        return maxsum;
    }
};
```
---
# 31-整数中1出现的次数
## 题目描述

求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

---


## 解题思路

### 暴力破解法

最简单直接的方法就是我们循环所有的1~n中的每个number，计算每个number出现的次数
此方法简单，容易理解，但它的问题是效率，时间复杂度为$O(N * logN)$，N比较大的时候，需要耗费很长的时间。
```C++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int count = 0;

        for(int i = 1;
            i <= n;
            i++)
        {
            count += NumberOf1(i);
        }

        return count;
    }
    int NumberOf1(int number)
    {
        int count = 0;
        while(number != 0)
        {
            if(number % 10 == 1)
            {
                count++;
            }
            number /= 10;
        }

        return count;
    }
};
```
---

### 分治法
我们重新分析下这个问题，

对于任意一个个位数n，只要n>=1,它就包含一个"1"；

n<1，即n=0时，则包含的"1"的个数为0。

于是我们考虑用分治的思想将任意一个n位数不断缩小规模分解成许多个个位数，这样求解就很方便。

但是，我们该如何降低规模？

仔细分析，我们会发现，

**任意一个n位数中"1"的个位可以分解为两个n-1位数中"1"的个数的和，最后再加上一个与最高位数相关的常数C**
例如，

> 对于n=12，可以拆分为01-09,10-12，即 f(12) = f(10 - 1) + f(12 - 10) + 3,其中3是表示最高位为1的数字个数，这里就是10,11,12；
>
> 对于n=132，可以拆分为0-99，100-132，即f(132)=f(100 -1) + f(132 - 100) + 33，33代表最高位为1的数字的个数，这里就是100~132百位数字的1出新了33次
>
> 对于232，可以拆分为0-99，100-232，即f(232) = 2*f(100 - 1) + f(32) + 100，因为232大于199，所以它包括了所有最高位为1的数字即100~199，共100个。

综上，我们分析得出，最后加的常数C只跟最高位n1是否为1有关

- 当最高位为1时，常数C为原数字N去掉最高位后剩下的数字+1，如N=12时，$C = 2 + 1 = 3$，N=132时，$C = 32 + 1 = 33$

- 当最高位大于1时，常数C为$10^(bit-1)$，其中bit为N的位数，如N=232时，bit=3，$C = 10^(bit-1) = 10^2 = 100$。 于是，我们可以列出递归方程如下：
```
if(n1 == 1)
    f(n) = f(10bit-1) + f(n - 10bit) + n - 10bit+ 1;
else
    f(n) = n1*f(10bit-1) + f(n – n1*10bit) + 10bit;
```
进一步可以归结为
```
f(n) = n1*f(10bit-1) + f(n – n1*10bit) + LEFT;
其中
if(n1 == 1)
    LEFT = n - 10bit+ 1;
else
    LEFT = 10bit;
```
此算法的优点是不用遍历1~N就可以得到f(N)。经过我测试，此算法的运算速度比解法一快了许多许多，数字在1010内时，算法都可以在毫秒级内结束。
```C++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        return CountOne(n);
    }
    int CountOne(int n){
        long count = 0;
        if(n < 1)
            count = 0;
        else if(n > 1 && n < 10)
            count = 1;
        else{// 计算n的位数
            long highest = n;//表示最高位的数字
            int bit = 0;
            while(highest >= 10){
                highest /= 10;
                bit++;
            }//  循环结束时, bit表示n的位数, 而highest是其最高位的数字
            int weight = pow(10, bit);//代表最高位的权重，即最高位一个1代表的大小
            if(highest == 1){
                count = CountOne(weight - 1)
                + CountOne(n - weight)
                + n - weight + 1;
            }
            else{
                count = highest * CountOne(weight - 1)
                + CountOne(n - highest * weight)
                + weight;
            }
        }
        return count;
    }
};
```
---
# 32-把数组排成最小的数
## 题目描述

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

---


## 解题思路

对vector容器内的数据进行排序，按照将a和b转为string后.若 a＋b<b+a  a排在在前 的规则排序,如 2 21 因为 212 < 221 所以 排序后为 21 2 ,to_string() 可以将int 转化为string

Tips:
>sort中的比较函数compare要声明为静态成员函数或全局函数，不能作为普通成员函数，否则会报错 。
因为：非静态成员函数是依赖于具体对象的，而std::sort这类函数是全局的，因此无法再sort中调用非静态成员函数。
静态成员函数或者全局函数是不依赖于具体对象的, 可以独立访问，无须创建任何对象实例就可以访问。
同时静态/全局函数 不可以调用类的非静态成员。
sort 是将数组里所有的数都按照这个规则排序了, 排序完成以后, 数组里面数的排列就已经是最小的数了, 
再用一个循环拼接成字符串就好了
---

```C++
using namespace std;
class Solution {
public:
    static bool com(int a, int b){//定义一个比较函数，作为参数传入sort函数中
        string str1 = to_string(a);
        string str2 = to_string(b);
        return (str1+str2) < (str2+str1);
    }
    string PrintMinNumber(vector<int> numbers){
        string res = "";
        if(numbers.empty()) return res;
        sort(numbers.begin(), numbers.end(), com);
        for(int i = 0; i < numbers.size(); i++){
            res += to_string(numbers[i]);
        }
        return res;
    }
};
```
---
# 33-丑数
## 题目描述

把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

---


## 解题思路

### 暴力破解法

最简单直接的方法，就是逐个判断每个整数是不是丑数，循环所有数字，判断它是不是丑数 首先我们需要判断某个整数number是不是丑数

```C++
#include<iostream>
#include<vector>
using namespace std;
#define __tmain main
bool IsUglyNum(int num) {
	while (num % 5 == 0)
		num /= 5;
	while (num % 3 == 0)
		num /= 3;
	while (num % 2 == 0)
		num /= 2;
	return (num == 1);
}
int GetUglyNumber_Solution(int index) {
	if (index < 1)
		return 0;
	int num = 0, count = 0;
	while (count < index) {
		num++;
		if (IsUglyNum(num))
			count++;
	}
	return num;
}
int __tmain() {
	int n,result;
	cin >> n;
	result = GetUglyNumber_Solution(n);
	cout << result << endl;
}
```
---

### 空间换时间法：时间效率较高

根据丑数的定义，丑数应该是另一个丑数乘以2、3或者5的结果（1除外）。 因此我们可以创建一个数组，里面的数字是排好序的丑数。里面的每一个丑数是前面的丑数乘以2、3或者5得到的。那关键就是确保数组里的丑数是有序的了。 
我们假设数组中已经有若干个丑数，排好序后存在数组中。我们把现有的最大丑数记做M。 现在我们来生成下一个丑数，该丑数肯定是前面某一个丑数乘以2、3或者5的结果。 
我们首先考虑把已有的每个丑数乘以2。在乘以2的时候，能得到若干个结果小于或等于M的。由于我们是按照顺序生成的，小于或者等于M肯定已经在数组中了，我们不需再次考虑； 我们还会得到若干个大于M的结果，但我们只需要第一个大于M的结果，因为我们希望丑数是按从小到大顺序生成的，其他更大的结果我们以后再说。 
我们把得到的第一个乘以2后大于M的结果，记为M2。同样我们把已有的每一个丑数乘以3和5，能得到第一个大于M的结果M3和M5。那么下一个丑数应该是M2、M3和M5三个数的最小者。

```C++
class Solution {
public:
    int ugly[10000];
    int min(int a, int b, int c){
        int tmp = (a < b ? a : b);
        return (tmp < c ? tmp: c);
    }
    int GetUglyNumber_Solution(int N){
        ugly[0] = 1;
        int index2 = 0, index3 = 0,index5 = 0;
        int index = 0;
        while(index < N){
            int val = min(ugly[index2] * 2,
                         ugly[index3] * 3,
                         ugly[index5] * 5);
            if(val == ugly[index2] * 2)
                index2++;
            if(val == ugly[index3] * 3)
                index3++;
            if(val == ugly[index5] * 5)
                index5++;
            ugly[++index] = val;
        }
        int res = ugly[N-1];
        return res;
    }
};
```
---
# 34-第一次只出现一次的字符


---

## 题目描述

在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.

---


## 解题思路

使用辅助数组进行计数，统计每个字符串的出现的次数，然后查找第一个只出现一次的字符位置

---

```C++
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.length() == 0)
            return -1;
        int count[256];
        //  将计数器数组清0
        memset(count, '\0', sizeof(count));
        //  对字符串中出现的每个字符进行计数
        for(int i = 0; i < str.length(); i++){
            count[(int) str[i]]++;
        }
        //  第一个只出现一次的字符
        for(int j = 0; j < str.length(); j++){
            if(count[str[j]] == 1)
                return j;
        }
        return -1;
    }
};
```
---
# 35-数组中的逆序对
## 题目描述

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

---


## 解题思路

### 暴力破解法

两个 for 循环枚举所有的数对，如果是逆序对，则 count++，最终返回 count 即可。时间复杂度$O(n^2)$，运行超时；

```C++
class Solution {
public:
    int InversePairs(vector<int> data) {
        if(data.empty()) return 0;
        int count = 0;
        for(int i = 0; i < data.size() - 1; i++){
            for(int j = i + 1; j < data.size(); j++){
                if(data[i] > data[j])
                    count++;
            }
        }
        count %= 1000000007;
        return count;
    }
};
```
---

### 排序--计算交换次数

> 考虑一下，逆序是说a[i]>a[j]，i<j。那么在排序的过程中，会把a[i]和a[j]交换过来，这个交换的过程，每交换一次，就是一个逆序对的“正序”过程。

**冒泡排序**
时间复杂度$O(n^2)$，运行超时；

```C++
class Solution {
public:
    int InversePairs(vector<int> data) {
        if(data.empty()) return 0;
        int count = 0;
        bool IsSwap = true;
        for(int i = 0; i < data.size() - 1; i++){
            IsSwap = false;
            for(int j = 0; j < data.size() - i - 1; j++){
                if(data[j] > data[j+1]){
                    swap(data[j], data[j+1]);
                    count++;
                    IsSwap = true;
                }
            }
            if(!IsSwap)
                break;
        }
        return count;
    }
};
```

**归并排序**

---
# 36-两个链表的第一个公共结点
## 题目描述

输入两个链表，找出它们的第一个公共结点。

---


## 解题思路

### 暴力破解法

最简单直接的方法就是，对于第一个链表的每个节点，我们依次判断其是不是第二条链表的公共结点

```C++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        ListNode *p1 = nullptr;
        ListNode *p2 = nullptr;
        //  循环第一个链表的每个结点
        for(p1 = pHead1; p1 != nullptr; p1 = p1->next){
             //  依次判断其在不在第二条链表中
            for(p2 = pHead2; p2 != nullptr; p2 = p2->next){
                if(p1 == p2)
                    break;
            }
            // 判断两个链表没有公共节点的请况就返回nullptr
            if(p1 == p2)
                break;
        }
        return p1;
    }
};
```
---

### 右对齐两个链表

如果两个链表有公共节点，则它们的形状必然是一个Y字形。

**长链表先走，实现右对齐**

先假设这两个链表的长度相等，则我们可以同步遍历这两个链表，找到公共节点。现在有两个链表，我们可以先分别求齐长度得其差n，然后遍历长的那个链表n个节点，然后同步遍历这两个链表即可。

```C++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        // 使得两个链表右对齐
        ListNode *p1 = pHead1;
        ListNode *p2 = pHead2;
        if(p1 == NULL || p2 == NULL)
            return NULL;
        int len1 = 0, len2 = 0;
        while(p1 != NULL){
            p1 = p1->next;
            len1++;
        }
        while(p2 != NULL){
            p2 = p2->next;
            len2++;
        }
        p1 = pHead1; 
        p2 = pHead2;
        if(len1 > len2){
             int sublen1 = len1 - len2;
             while(sublen1 > 0){
                p1 = p1->next;
                 sublen1--;
            }
        }
        else{
            int sublen2 = len2 - len1;
             while(sublen2 > 0){
                p2 = p2->next;
                 sublen2--;
            }
        }
        while(p1 != NULL && p2 != NULL){
            if(p1 == p2){
                break;
            }
            p1 = p1->next;
            p2 = p2->next;
        }
        return p1;
    }
}
```
---
# 37-数字在排序数组中出现的次数
## 题目描述

统计一个数字在排序数组中出现的次数。

---

## 解题思路

### 二分查找

二分查找找到数字在有序数组中的其中一个位置，然后以该位置为中心，分别往左往右线性遍历，计算该数字出现的次数

```C++
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int key) {
        if(data.empty()) return 0;
        //  用二分查找查找到Key的位置
        int index = BinarySearch(data, 0, data.size() - 1, key);
        if(index == -1) return 0;
        int count = 1;
        // 查找前面部分Key的个数
        for(int i = index - 1; i >= 0 && data[i] == key; i--){
            count++;
        }
        // 查找后面部分Key的个数
        for(int j = index + 1; j < data.size() && data[j] == key; j++)
            count++;
        return count;
    }
    int BinarySearch(vector<int> data, int begin, int end, int target){
        if(begin > end)
            return -1;
        while(begin <= end){
            int mid = begin + (end - begin) / 2;
            if(data[mid] == target) return mid;
            else if(data[mid] > target){
                end = mid - 1;
            }
            else if(data[mid] < target) {
                begin = mid + 1;
            }
        }
        return -1;
    }
};
```
---
# 38-二叉树的深度

## 题目描述

输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

---

## 解题思路
### 递归

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if(!pRoot) return 0;
        else{
            int leftdepth = TreeDepth(pRoot->left);
            int rightdepth = TreeDepth(pRoot->right);
            return max(leftdepth,rightdepth) + 1;
        }
    }
};
```
### 非递归(使用队列)

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if(!pRoot) return 0;
        queue<TreeNode* > q;
        q.push(pRoot);
        int level = 0;
        while(!q.empty()){
            int len = q.size();
            level++;
            while(len--){
                TreeNode* node = q.front();
                q.pop();
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
        }
        return level;
    }
};
```
---
# 39-平衡二叉树

## 题目描述

输入一棵二叉树，判断该二叉树是否是平衡二叉树。

---

## 解题思路

这题可以使用剑指offer第38题计算二叉树最大深度的思路，使用递归，如果结点的左右子树的深度绝对值差小于等于1，那么判定该节点下左右子树是平衡的，继续递归判断该节点的左右子树是否是平衡二叉树；如果结点的左右子树的深度绝对值差大于1，则判断不是平衡二叉树。

```C++
class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        if(!pRoot) return true;
        int leftdepth = TreeDepth(pRoot->left);
        int rightdepth = TreeDepth(pRoot->right);
        if(abs(leftdepth-rightdepth) <= 1){
            return IsBalanced_Solution(pRoot->left) && IsBalanced_Solution(pRoot->right);
        }
        else
            return false;
    }
    int TreeDepth(TreeNode* root){
        if(!root) return 0;
        int leftdepth = TreeDepth(root->left);
        int rightdepth = TreeDepth(root->right);
        return max(leftdepth,rightdepth) + 1;
    }
};
```
---
# 40-数组中只出现一次的数字

## 题目描述
一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

---
## 解题思路

此题用了两次异或运算特点：

第一次使用异或运算，得到了两个只出现一次的数相异或的结果。

因为两个只出现一次的数肯定不同，即他们的异或结果一定不为0，一定有一个位上有1。

另外一个此位上没有1，我们可以根据此位上是否有1，将整个数组重新划分成两部分，

一部分此位上一定有1，另一部分此位上一定没有1，

然后分别对每部分求异或，因为划分后的两部分有这样的特点：
其他数都出现两次，只有一个数只出现一次。因此，我们又可以运用异或运算，分别得到两部分只出现一次的数。

```C++
class Solution {
public:
    //定义变量为8字节数
    #define INT_SIZE (sizeof(int) * 8) 
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        if(data.size() < 2)
            return ;
        *num1 = *num2 = 0;
        int XOR = data[0];
        //数组中所有的数都做异或，最后得到那两个数字的异或的结果
        for(int i = 1; i < data.size(); i++){
            XOR ^= data[i];
        }
        //找到1的位置
        int index = 0,temp = 1;
        for(;index < INT_SIZE; index++){
            if((XOR & temp)){
                break;
            }
            temp <<= 1;
        }
        if(index == INT_SIZE){
            return ;
        }
        //  于是我们知道那两个只出现一次的数字, 第index位必然不相同
        //  因此 我们将数组划分成两部分
        //  一部分index位是0
        //  另外一部分index位是1
        for(int j = 0; j< data.size(); j++){
            if(IsBit(data[j], index)){
                *num1 ^= data[j];
            }
            else{
                *num2 ^= data[j];
            }
        }
    }
private:
    // 判断第index位是否为1
    bool IsBit(int num, int index){
        num = num >> index;
        return (num & 1);
    }
};
```
---
# 41-和为S的连续正数序列

## 题目描述

小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

**输出描述**
>输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序

---

## 解题思路
使用双指针，begin指针指向序列最前面一位，end指针指向序列最后面一位。如果满足和为S，则依次压入一维数组curr中，再把curr数组压入二维数组res中，序列同时end++，再添加end后面一位；如果和大于S，则删掉序列最前面一位，同时begin++;如果和小于S，则end++，同时添加end后面一位；


```C++
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int> > res;
        vector<int> curr;
        if(sum < 3) return res;
        int begin = 1,end = 2, mid = (sum + 1) / 2;
        int currsum = begin + end;
        while(begin < mid && end < sum){
            if(currsum == sum){//  和正好是sum的话, 就存储下来
                curr.clear();
                for(int i = begin;i <= end;i++){
                    curr.push_back(i);
                }
                res.push_back(curr);
                //  存储完以后, 进一步往下走
                end++;
                currsum += end;
            }
            else if(currsum > sum){// 如果和太大了, 缩短起始位置
                currsum -= begin;
                begin++;
            }
            else{//  如果和太小了, 那么增加结束位置
                end++;
                currsum += end;
            }
            
        }
        return res;
    }
};
```
---
# 42-和为S的两个数字

## 题目描述

输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

**输出描述**

>对应每个测试案例，输出两个数，小的先输出。
---
## 解题思路

使用双指针，分别从有序数组的两端开始遍历相加，如果满足和为S的话就压入结果数组，因为得到的两数乘积是最小的，乘积最大的是最中间的和为S的两个数；

```c++
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        vector<int> res;
        if(array.size() < 2)
            return res;
        int begin = 0, end = array.size() - 1;
        while(begin < end){
            int currsum = array[begin] + array[end];
            if(currsum == sum){// 分别从两端开始，这样第一对满足的数字的乘积就是最小的
                res.push_back(array[begin]);
                res.push_back(array[end]);
                break;
            }
            else if(currsum > sum){
                end--;
            }
            else if(currsum < sum){
                begin++;
            }
        }
        return res;
    }
};
```
---
# 43-左旋转字符串

## 题目描述

汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

---

## 解题思路

通过翻转直线循环移位
通过reverse操作 一个序列abcdefg，如果向左循环移动n位， 例如3位，则会编程(defg)(abc)，我们把原序列分成两部分，A=(abc)，B=(defg)，原序列为AB，我们要的结果为BA，则可以这么做：(ATBT)T =BA 假设原序列有n位，循环左移i位的过程如下：

reverse(0,i-1);

reverse(i,n-1);

reverse(1,n-1);

例如原序列：abcdefg，循环左移3位：
ATB
>abc defg -=> cba defg

ATBT

>cba defg -=> cba gfed

(ATBT)T

>cba gfed -=> defg abc

---
```c++
class Solution {
public:
    string LeftRotateString(string str, int n) {
        /// abcXYZdef
        /// abcXYZdefabcXYZdef
        /// 012345678
        /// 新的串是两倍串第len位起始长度为n字串
        /*
        int length = str.size();
        if(length == 0)
            return "";
        n = n % length;
        str += str;
        return str.substr(n, length);
        */
        if(str.empty())
            return "";
        n %= str.size();
        for(int i=0,j=n-1;i<j;i++,j--) swap(str[i],str[j]);
        for(int i=n,j=str.size()-1;i<j;i++,j--) swap(str[i],str[j]);
        for(int i=0,j=str.size()-1;i<j;i++,j--) swap(str[i],str[j]);
        return str;
    }
};
```
---
# 44-反转单词顺序列

## 题目描述

牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？

## 解题思路
### 思路一：使用两个辅助栈stk1,stk2

通过将字符串压入栈中, 现在出栈顺序正好是入栈顺序的逆序
即我们实现了一次整个字符串的翻转
接下来我们翻转每个单词
只要不是空格就一直入栈(实现翻转)
遇见空格的时候，就读取栈中元素(出栈的顺序正好是每个单词的顺序)

```c++
#include <stack>
class Solution {
public:
    string ReverseSentence(string str) {
        if(str.size() == 0)
            return str;
        stack<char> stk1;
        stack<char> stk2;
        string result;
        for(int i = 0; i < str.size(); i++){
            stk1.push(str[i]);
        }
        //  通过将字符串压入栈中, 现在出栈顺序正好是入栈顺序的逆序
        //  即我们实现了一次整个字符串的翻转

        //  接下来我们翻转每个单词
        //  只要不是空格就一直入栈(实现翻转)
        //  遇见空格的时候，就读取栈中元素(出栈的顺序正好是每个单词的顺序)
        while(!stk1.empty()){
            if(stk1.top() != ' '){//没有遇到空格 就再弹出 压到第二个栈
                stk2.push(stk1.top());
                stk1.pop();
            }
            else{//如果遇到了空格 一个单词结束了  将第二个栈里的弹出
                while(!stk2.empty()){
                    result += stk2.top();
                    stk2.pop();
                }
                result += stk1.top();//空格也要加上
                stk1.pop();//不要忘记！！！
            }
        }
        while(!stk2.empty()){//此时stk1已经空了 上面的循环进不去了 但是stk2中还有最后一个单词
            result += stk2.top();
            stk2.pop();
        }
        return result;
    }
};
```
### 思路二：从后向前重新组装字符串

还有以一种思路就是从后向前，读取到每个单词，就开始顺序的拼接它们

```c++
class Solution
{
public:
    string ReverseSentence(string str)
    {
        if(str.size( ) == 0)
        {
            return str;
        }
        string res = "", tmp = "";
        for(unsigned int i = 0; i < str.size(); ++i)
        {
            if(str[i] == ' ')       //  发现一个单词
            {
                res = " " + tmp + res;      //  顺序的拼接, 前面需要一个空格
                tmp = "";
            }
            else
            {
                tmp += str[i];
            }
        }
        if(tmp.size() != 0)     //  拼接最后一个单词, 前面无需空格
        {
            res = tmp + res;
        }
        return res;
    }
};
```
---
# 45-扑克牌顺子

## 题目描述

LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

## 解题思路

可以把5张牌看成由5个数字组成的数组。大、小王是特殊的数字，我们不妨把它们定义为0，这样就能和其他扑克牌区分开来了。

接下来我们分析怎样判断5个数字是不是连续的，最直观的方法是把数组排序。值得注意的是，由于0可以当成任意数字，我们可以用0去补满数组中的空缺。如果排序之后的数组不是连续的，即相邻的两个数字相隔若干个数字，但只要我们有足够的0可以补满这两个数字的空缺，这个数组实际上还是连续的。举个例子，数组排序之后为{0，1，3，4，5}，在1和3之间空缺了一个2，刚好我们有一个0，也就是我们可以把它当成2去填补这个空缺。

于是我们需要做3件事：

    1.首先把数组排序

    2.再统计数组中的0的个数

    3.最后统计排序之后的数组中相邻数字之间的空缺总数。

如果空缺的总数小于或者等于0的个数，那么这个数组就是连续的；反之则不连续。

最后，我们还需要注意一点：

>如果数组中的非0数字重复出现，则该数组不是连续的。

---

```c++
#include <algorithm>
class Solution {
public:
    bool IsContinuous( vector<int> numbers ) {
        if(numbers.empty()) return false;
        int zeros = 0;
        sort(numbers.begin(), numbers.end());//给数组排序
        while(numbers[zeros] == 0){//计算前面0的个数
            zeros++;
        }
        //  然后看0能不能填补两个数之间的空缺
        for(int i = zeros + 1; i < numbers.size(); i++){
            // 如果数组中的非0数字重复出现，则该数组不是连续的
            if(numbers[i] == numbers[i - 1])
                return false;
            else{//  否则填补空缺, 无空缺的情况不用单独判断(空缺为0)
                zeros -= (numbers[i] - numbers[i - 1] - 1); 
            }
        }
        if(zeros >= 0)
            return true;
        else
            return false;
    }
};
```
---
# 46-孩子们的游戏(圆圈中最后剩下的数)

## 题目描述

每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1)

## 解题思路

**思路一：模拟环形链表**

既然题目中有一个数字圆圈，很容易想到用环形链表来模拟这个圆圈。我们可以创建一个有n个结点的环形链表，然后每次在这个链表中删除第m个结点。

可以使用STL中的list或者vector来模拟一个环形链表。stl本身不是环形结构，所以每次当迭代器扫描到链表的末尾时，我们要将迭代器移到链表头。

```c++
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n < 1 || m < 1) return -1;
        vector<int> nums;
        int cntN = 0, cntM = -1;
        for(int i = 0; i < n; i++){
            nums.push_back(i);
        }
        while(nums.size() > 1){
            for(int cntM = 1; cntM < m; cntM++){
                //每次删除元素后容器后面元素会自动向前补齐位置，所以只要遍历m-1位就行了
                cntN++;
                cntN %= nums.size();
            }
            nums.erase(nums.begin() + cntN);//删除容器中循环位置中的第m位数
        }
        return nums[0];
    }
}
```

**思路二：递推公式-获取最后的胜利者的序号**

解法1：找规律。首先定义最初的n个数字（0,1,…,n-1）中最后剩下的数字是关于n和m的方程为f(n,m)。在这n个数字中，第一个被删除的数字是（m-1）%n，为简单起见记为k。那么删除k之后的剩下n-1的数字为0,1,…,k-1,k+1,…,n-1，并且下一个开始计数的数字是k+1。相当于在剩下的序列中，k+1排到最前面，从而形成序列k+1,…,n-1,0,…k-1。该序列最后剩下的数字也应该是关于n和m的函数。由于这个序列的规律和前面最初的序列不一样（最初的序列是从0开始的连续序列），因此该函数不同于前面函数，记为f’(n-1,m)。最初序列最后剩下的数字f(n,m)一定是剩下序列的最后剩下数字f’(n-1,m)，所以f(n,m)=f’(n-1,m)。接下来我们把剩下的的这n-1个数字的序列k+1,…,n-1,0,…k-1作一个映射，映射的结果是形成一个从0到n-2的序列：
```
k+1    ->   0
k+2    ->   1
…
n-1    ->   n-k-2
0      ->   n-k-1
…
k-1    ->   n-2
```
把映射定义为$p$，则$p(x)= (x-k-1)%n$，即如果映射前的数字是$x$，则映射后的数字是$(x-k-1)%n$。对应的逆映射是$p-1(x)=(x+k+1)%n$。由于映射之后的序列和最初的序列有同样的形式，都是从0开始的连续序列，因此仍然可以用函数f来表示，记为f(n-1,m)。根据我们的映射规则，映射之前的序列最后剩下的数字$f’(n-1,m)= p-1 [f(n-1,m)]=[f(n-1,m)+k+1]%n$。把$k=m%n-1$代入得到$f(n,m)=f’(n-1,m)=[f(n-1,m)+m]%n$。

经过上面复杂的分析，我们终于找到一个递归的公式。要得到n个数字的序列的最后剩下的数字，只需要得到n-1个数字的序列的最后剩下的数字，并可以依此类推。当n=1时，也就是序列中开始只有一个数字0，那么很显然最后剩下的数字就是0。我们把这种关系表示为：
```
               0                 n=1
f(n,m)={
              [f(n-1,m)+m]%n     n>1
```
尽管得到这个公式的分析过程非常复杂，但它用递归或者循环都很容易实现。最重要的是，这是一种时间复杂度为$O(n)$，空间复杂度为$O(1)$的方法，因此无论在时间上还是空间上都优于前面的思路。

```c++
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        // 递归版
        if(n < 1 || m < 1)
            return -1;
        else if(n == 1)
            return 0;
        else{
            // F[n] = (F[n - 1] + m) % n
            return (LastRemaining_Solution(n - 1, m) + m) % n;
        }
        // 迭代版
        /*
        if(n < 1 || m < 1)
            return -1;
        int last = 0;
        for(int step = 2; step <= n; step++){
            last = (last + m ) % step;
        }
        return last;
        */
    }
};
```
---
# 47-求1+2+3+...+n

## 题目描述
求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句(A?B:C).

## 解题思路

**思路一：递归短路**

计算1+2+3+...+n, 可以认为是一个递归的过程, 这点很容易理解

但是怎么不用分支判断来保证递归的终止呢?
我们可以通过短路来实现循环终止,

- 从n开始递减,进行递归的相加运算
- 当递归至0时使递归短路即可

```c++
class Solution {
public:
    int Sum_Solution(int n) {
        int ans = n;
        n && (ans += Sum_Solution(n - 1));//当n为0时，相加语句短路，返回0；
        return ans;//不为0时返回n与n-1相加的结果
    }
}
```

**思路二：位运算计算$1+2+3+...+n=n(n+1)/2$**
我们其实知道，等差数列求和的问题

$1+2+3+...+n=\frac{n(n+1)}{2}$

但是$\frac{n(n+1)}{2}$是一个乘除的运算, 本题限制使用乘除，因此我们得另寻它法

我们知道a*b运算在计算机内部其实是通过移位和加法来完成的
```c++
int Multi(int a, int b)
{
    int res = 0;
    while(a != 0)
    {
        if((a & 1) != 0)
        {
            res += b;
        }
        a >>= 1;
        b <<= 1;

    }

    return res;
}
```
那么我们的结果就是

Multi(n, n + 1) >>1 但是我们的乘法函数中仍然有循环和判断, 因此我们需要进一步优化,

还是上面的策略，

循环用递归来代替
分支用短路运算来代替
那么我们的乘法运算就成为
```c++
int MultiRecursion(int a, int b)
{
    static int res = 0;
    a && MultiRecursion(a >> 1, b << 1);    //  递归的进行运算
    (a & 1) && (res += b);                  //  短路

    return res;
}
```

最终代码如下：
```c++
class Solution
{
public:
    int res;
    int Sum_Solution(int n)
    {
        res = 0;
        return (MultiRecursion(n, n + 1) >> 1);
    }

    int MultiRecursion(int a, int b)
    {
        a && MultiRecursion(a >> 1, b << 1);    //  递归的进行运算

        (a & 1) && (res += b);                  //  短路


        return res;
    }

};
```
---
# 48-不用加减乘除做加法

## 题目描述
写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

## 解题思路
位运算模拟加法

**怎么模拟加法**

>用异或可以模拟不带进位的加法运算, 因为异或相同则0, 不同则1, 正好与不带进位的加法结果相同

**那么怎么判断出进位呢**

>进位的产生主要是1+1=10, 我们看到与操作可以判断两个位是不是需要进位,
>可见用与运算可以模拟出每一位的是否有进位

**但是怎么表示进位呢?**

>进位无非是进到上一位去, 因此我们直接与的结果左移一位即得进位

由此我们得到了两个数:

- 一个是异或运算得出的不带进位的结果,
- 一个是两个相加的所有进位
发现什么, 我们反复的将两个数进行加法操作, 直到没有进位的时候, 那么结果就是我们想要的

过程如下

首先看十进制是如何做的： 5+7=12，三步走

- 相加各位的值，不算进位，得到5 + 7 = 2（不算进位）

- 计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。

- 重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。

我们可以用三步走的方式计算二进制值相加：

5 -=> 101, 7 -=> 111

- 相加各位的值，不算进位，得到010，二进制每位相加就相当于各位做异或操作，101^111=010

- 计算进位值，得到1010，相当于各位做与操作得到101，再向左移一位得到1010，(101&111)<<1。

- 重复上述两步， 各位相加 010^1010=1000，进位值为100=(010&1010)<<1。

- 继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。

```c++
class Solution
{
public:
    int Add(int left, int right)
    {
        int temp;
        while(right != 0)
        {
            temp = left ^ right;                //  计算不带进位的情况
            right = (left & right) <<1;         //  计算带进位的情况
            left = temp;
            //  now left = 不带进位的情况, right = 带进位的情况
        }

        return left;
    }
};
```
---
# 49-把字符串转换成整数

## 题目描述
将一个字符串转换成一个整数(实现Integer.valueOf(string)的功能，但是string不符合数字要求时返回0)，要求不能使用字符串转换整数的库函数。 数值为0或者字符串不是一个合法的数值则返回0。

**输入描述:**
>输入一个字符串,包括数字字母符号,可以为空

**输出描述:**
>如果是合法的数值表达则返回该数字，否则返回0

## 解题思路

设置一个flag判断是否有符号位，如果为'-'，最后结果乘以-1；
条件循环中判断每一个字符减去'0'得到的元素是否是大于等于0小于等于9的整数；

```c++
class Solution {
public:
    int StrToInt(string str) {
        if(str.empty())
            return 0;
        long res = 0;
        int flag = 1;
        if(str[0] == '-')//
            flag = -1;
        for(int i = (str[0] ==  '-' || str[0] == '+') ? 1 : 0; i < str.size(); i++){
            int num = str[i] - '0';
            if(num >= 0 && num <= 9){
                res = 10 * res + num;
            }
            else{
                return 0;
            }
        }
        res *= flag; 
        return res ;
    }
};
```
---
# 50-数组中重复的数字

## 题目描述

在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

## 解题思路
**思路一：辅助数组**
利用辅助数组做标记
```c++
class Solution {
public:
    bool duplicate(int numbers[], int length, int* duplication) {
        if(length<=1) return false;
        vector<int> numbers_index(length,0);
        for(int i = 0; i < length; i++){
            numbers_index[numbers[i]]++;
            
            if(numbers_index[numbers[i]] > 1){
                *duplication = numbers[i];
                return true;
            }
        }
        return false;
    }
};
```

**思路二：用原始数组做标记**

```c++
class Solution {
public:
    // Parameters:
    //        numbers:     an array of integers
    //        length:      the length of array numbers
    //        duplication: (Output) the duplicated number in the array number
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    bool duplicate(int numbers[], int length, int* duplication) {
        for(int i = 0; i < length; ++i){
            int index = numbers[i] % length;//防止数组越界
            if(numbers[index] >= length){
                *duplication = index;
                return true;
            }              
            numbers[index] += length;  
        }
        return false;
    }
};
```
*Tips:*
```
int index = numbers[i] % length;这句话的作用是防止数组越界
比如数组[2,3,2,0]
i=0,index=2,numbers[2]=6;
i=1,index=3,numbers[3]=4;
i=2,index=2,numbers[2]=6;这时候会发生越界，所以对length取余；
```
---
# 51-构建乘积数组

## 题目描述

给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。

## 解题思路

<分析>：
解释下代码，设有数组大小为5。
对于第一个for循环
第一步：b[0] = 1;
第二步：b[1] = b[0] * a[0] = a[0]
第三步：b[2] = b[1] * a[1] = a[0] * a[1];
第四步：b[3] = b[2] * a[2] = a[0] * a[1] * a[2];
第五步：b[4] = b[3] * a[3] = a[0] * a[1] * a[2] * a[3];
然后对于第二个for循环
第一步
temp *= a[4] = a[4]; 
b[3] = b[3] * temp = a[0] * a[1] * a[2] * a[4];
第二步
temp *= a[3] = a[4] * a[3];
b[2] = b[2] * temp = a[0] * a[1] * a[4] * a[3];
第三步
temp *= a[2] = a[4] * a[3] * a[2]; 
b[1] = b[1] * temp = a[0] * a[4] * a[3] * a[2];
第四步
temp *= a[1] = a[4] * a[3] * a[2] * a[1]; 
b[0] = b[0] * temp = a[4] * a[3] * a[2] * a[1];
由此可以看出从b[4]到b[0]均已经得到正确计算。



```c++
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        vector<int> B;
        int n = A.size();
        if(A.empty()) return B;
        B.push_back(1);
        for(int i = 0; i < n - 1; i++){ 
            B.push_back(B.back() * A[i]);
        }
        int tmp = 1;
        for(int j = n - 1; j >= 0; j--){
            B[j] *= tmp;
            tmp *= A[j];
        }
        return B;
    }
};
```
---
# 52-正则表达式匹配

## 题目描述

请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配

## 解题思路

首先，考虑特殊情况：
- 1.两个字符串都为空，返回true
- 2.当第一个字符串不空，而第二个字符串空了，返回false（因为这样，就无法匹配成功了,而如果第一个字符串空了，第二个字符串非空，还是可能匹配成功的，比如第二个字符串是“a*a*a*a*”,由于‘*’之前的元素可以出现0次，所以有可能匹配成功）

之后就开始匹配第一个字符，这里有两种可能：匹配成功或匹配失败。但考虑到pattern
下一个字符可能是‘*’， 这里我们分两种情况讨论：

pattern下一个字符为‘*’或不为‘*’：
- 1.pattern下一个字符不为‘*’：这种情况比较简单，直接匹配当前字符。如果匹配成功，继续匹配下一个；如果匹配失败，直接返回false。注意这里的“匹配成功”，除了两个字符相同的情况外;还有一种情况，就是pattern的当前字符为‘.’,同时str的当前字符不为‘\0’。
- 2.pattern下一个字符为‘*’时，稍微复杂一些，因为‘*’可以代表0个或多个。
这里把这些情况都考虑到：
    - a.当‘*’匹配0个字符时，str当前字符不变，pattern当前字符后移两位，跳过这个‘*’符号；
    - b.当‘*’匹配1个或多个时，str当前字符移向下一个，pattern当前字符不变。（这里匹配1个或多个可以看成一种情况，因为：当匹配一个时，由于str移到了下一个字符，而pattern字符不变，就回到了上边的情况a；当匹配多于一个字符时，相当于从str的下一个字符继续开始匹配）。

```c++
class Solution {
public:
    bool match(char* str, char* pattern)
    {
        if(*str == '\0' && *pattern == '\0') return true;
        if(*str != '\0' && *pattern == '\0') return false;
        if(*(pattern+1) != '*'){
            if(*str == *pattern || (*str != '\0' && *pattern == '.')){
                return match(str+1,pattern+1);
            }
            else{
                return false;
            }
        }
        else{
            if(*str == *pattern || (*str != '\0' && *pattern == '.'))
                return match(str+1,pattern) || match(str,pattern+2);//考虑匹配0位的请况
            else
                return match(str,pattern+2);
        }
    }
};
```
---

# 53-表示数值的字符串

## 题目描述
请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

## 解题思路

按照样例提示，一步一步排除；可以设置bool变量标记E，符号，小数点是否出现过
- 1.排除E或e的请况，E或者e的后面不能为空，E不能重复出现
- 2.排除符号的请况，第二次出现'-','+'，要在'e'后面出现，第一次出现符号，且不在开头，则必须要在e后面
- 3.排除小数点的请况，e后面不能接小数点，小数点不能出现两次
- 4.考虑其他非数值字符的请况
- 5.考虑字符串只有一位'-'或者'+'的字符的请况

```c++
class Solution {
public:
    bool isNumeric(char* string)
    {
        if(strlen(string) == 1 && (string[0] == '-' || string[0] == '+'))
            return false;//考虑字符串只有一位'-'或者'+'的字符的请况
        // 标记E，符号，小数点是否出现过
        bool hasE = false, decimal = false, sign = false;
        for(int i = 0; i < strlen(string); i++){
            if(string[i] == 'e' || string[i] == 'E'){//E或者e的后面不能为空
                if(i == strlen(string) - 1)
                    return false;
                if(hasE) //E不能重复出现
                    return false;
                hasE = true;
            }
            else if(string[i] == '-' || string[i] == '+'){
                if(sign && (string[i-1] != 'e' && string[i-1] != 'E'))
                    return false; //第二次出现'-','+'，要在'e'后面出现
                if(!sign && i > 0 && (string[i-1] != 'e' && string[i-1] != 'E'))
                    return false;//第一次出现符号，且不在开头，则必须要在e后面
                
                sign = true;
            }
            else if(string[i] == '.'){
                if(decimal || hasE)// e后面不能接小数点，小数点不能出现两次
                    return false;
                decimal = true;
            }//前面已经考虑过合法的非数字的字符了，后面如果字符不是0-9之间就为非数值字符串
            else if(string[i] < '0' || string[i] > '9')
                return false;
        }
        return true;
    }

};
```
---

# 54-字符流中第一个不重复的字符

## 题目描述
请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

**输出描述:**
> 如果当前字符流没有存在出现一次的字符，返回#字符。

## 解题思路

利用哈希表的思想，创建count数组对出现过的字符计数，同时创建一个字符串str将出现过的字符连接起来，最后遍历str，返回count数组第一个计数为1的字符；

```c++
#include<string>
using namespace std;
class Solution
{
public:
    int count[256] = {0};
    string str = "";
    void Insert(char ch)
    {
        str += ch;
        count[(int)ch]++;
    }
  //return the first appearence once char in current stringstream
    char FirstAppearingOnce()
    {
        for(int i = 0; i < str.size(); i++){
            if(count[(int)str[i]] == 1)
                return str[i];
        }
        return '#';
    }
};
```
---
# 55-链表中环的入口结点

## 题目描述
给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

## 解题思路

**思路一：**
假设x为环前面的路程（**黑色路程**），a为环入口到相遇点的路程（**蓝色路程**，假设顺时针走）， c为环的长度（**蓝色+橙色路程**）
![avatar](D:/Projects/myblog/source/_posts/img/剑指55.png)
当快慢指针相遇的时候：

此时慢指针走的路程为$slow = x + m * c + a$,快指针走的路程为$fast = x + n * c + a$;
$2 slow = fast$,$2 * ( x + m*c + a ) = (x + n *c + a)$
从而可以推导出：
$x = (n - 2 * m )*c - a = (n - 2 *m -1 )*c + c - a$
即环前面的路程 = 数个环的长度（为可能为0） + $c - a$
什么是c - a？这是相遇点后，环后面部分的路程。（**橙色路程**）
所以，我们可以让一个指针从起点A开始走，让一个指针从相遇点B开始继续往后走，
2个指针速度一样，那么，当从原点的指针走到环入口点的时候（此时刚好走了x）
从相遇点开始走的那个指针也一定刚好到达环入口点。
所以2者会相遇，且恰好相遇在环的入口点。

最后，判断是否有环，且找环的算法复杂度为：
**时间复杂度：$O(n)$,空间复杂度：$O(1)$**

```c++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        if(pHead == NULL && pHead->next == NULL)
            return NULL;
        ListNode* fast = pHead;
        ListNode* slow = pHead;
        while(fast->next != NULL && slow != NULL){
            fast = fast->next->next;
            slow = slow->next;
            if(slow == fast){
                fast = pHead;
                while(fast != slow){
                    fast = fast->next;
                    slow = slow->next;
                }
                if(fast == slow)
                    return slow;
            }
        }
        return NULL;
    }
}
```

**思路二：断链法**

时间复杂度为$O（n）$，两个指针，一个在前面，另一个紧邻着这个指针，在后面。 
两个指针同时向前移动，每移动一次，前面的指针的next指向NULL。 
也就是说：访问过的节点都断开，最后到达的那个节点一定是尾节点的下一个， 也就是循环的第一个。 
这时候已经是第二次访问循环的第一节点了，第一次访问的时候我们已经让它指向了NULL.
所以到这结束。

**但是这种方法修改了链表的指向**

```c++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        if(pHead == nullptr || pHead->next == nullptr)
            return nullptr;
        ListNode* current = pHead->next;
        ListNode* previous = pHead;
        while(current != nullptr){
            previous->next = nullptr;
            previous = current;
            current = current->next;
        }
        return previous;
    }
}
```
---
# 56-删除链表中重复的结点

## 题目描述
在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

## 解题思路

```c++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead)
    {
        ListNode* L = new ListNode(-1);
        ListNode* newNode = L;
        ListNode* preNode = NULL;
        ListNode* pNode = pHead;
        int count;
        while(pNode != NULL){
            //如果当前结点是重复结点则下移一位
            preNode = pNode;
            pNode = pNode->next;
            count = 0;
            while(pNode && preNode->val == pNode->val){
                count++;
                pNode = pNode->next;
            }
            if(count == 0){//如果不重复
                newNode->next = preNode;
                newNode = preNode;
            }
        } 
        newNode->next = NULL;
        return L->next;
    }
};
```
