---
title: 剑指offer
date: 2017-01-02 09:57:34
updated: 2017-01-02 09:57:34
tags:
  - 算法题
categories:
  - 面试
toc: true
---
剑指offer一刷留念

<!-- more -->

# 剑指offer

> A：完全不理解写不出，B：思想理解写不出，C：思想不理解提示能写出

## 一刷

### 1. 二维数组中的查找(C)

> 在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

思路：因为矩阵的特殊性，所以先从**第一列最后一行**开始比较，小于则向上查找，大于则向后查找

### 2.替换空格(B)

> 请实现一个函数，将一个字符串中的空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

思路：使用正则表达式，**\s+代表多个空格，？则表示取尽可能少的空格**，然后通过replace函数替换为%20

```javascript
return str.replace(/\s+?/g,'%20')
```

### 3.从尾到头打印链表

>输入一个链表，从尾到头打印链表每个节点的值。

思路1：先将链表每个结点的值存入数组中，然后通过数组的**reverse**方法，即可从尾到头打印

我的思路：直接用**unshift**

```javascript
res.unshift(head.val);
```

### 4.重建二叉树(B)

> 输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

思路：二叉树前序遍历第一个点为根节点，中序遍历顺序为先左子树然后根节点最后右子树。所以**先通过前序遍历找出根节点，然后将中序遍历分为左右子树两组，最后对于每个子树依次递归调用。**

```javascript
function reConstructBinaryTree(pre, vin)
{
    // write code here
    if(pre.length==0 || vin.length==0) return null;
    var index=vin.indexOf(pre[0]);
    var left=vin.slice(0,index);//中序左子树
    var right=vin.slice(index+1);//中序右子树
    return {
        val:pre[0],
        //递归左右子树的前序，中序 
        left:reConstructBinaryTree(pre.slice(1,index+1),left),
        right:reConstructBinaryTree(pre.slice(index+1),right)
     }; 
}
```

### 5.用两个栈实现队列

> 用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型

思路：push + shift

### 6. 旋转数组的最小数字

>把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

思路：

1. Math.min
2. 排序:直接将数组按从小到大排序，返回第一个元素即可。
3. 二分法:mid = Math.floor((left+right)/2),直到左右差1个元素

```javascript
return Math.min.apply(Math,rotateArray);
```

### 7.斐波那契数列(C)

> 输入一个整数n，请你输出斐波那契数列的第n项。

思路：F(n) = F(n-2)+F(n-1)

1. 递归版本：不好
2. **动态规划版本**

```javascript
//2.动态规划版本：
for(var i=2;i<=n;i++)
{
  temp = f1 + f2;
  f1=f2;
  f2 = temp;
}
```

### 8.跳台阶

> 一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

思路：和7题本质一样

### 9.变态跳台阶

> 一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

思路：**F(n) = 2* F(n-1)**

```javascript
return 2 * jumpFloorII(number - 1)
```

### 10.矩形覆盖

> 我们可以用`2*1`的小矩形横着或者竖着去覆盖更大的矩形。请问用n个`2*1`的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

思路：依旧是斐波那契数列

### 11.二进制中1的个数(C)

> 输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

思路:

1. **把一个整数减去1，再和原来的整数做与运算**，会把该整数最右边的一个1变为0。那么一个整数的二进制表示中有多少个1，就可以进行多少次这样的操作。
2. 如果是负数，对其进行位操作,然后**toString**

```javascript
//思路1:n=n&(n-1);
//思路2: n = n>>>0;;n.toString(2); 
```

### 12.数值的整数次方

> 给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

思路:

1. 先判断浮点数的正负情况，然后通过累乘的方式, 注意exponent正负
2. `return base**exponent;`

### 13.调整数组顺序

> 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

思路: 通过map函数

```javascript
array.map(a => (a%2!=0?arr1.push(a):arr2.push(a)));
```

### 14.链表中倒数第k个结点

> 输入一个链表，输出该链表中倒数第k个结点。

思路: **通过prev和tail来获取距离为k的一段**，然后就tail指向链表最后，

### 15.反转链表

> 输入一个链表，反转链表后，输出链表的所有元素。

思路：**三个指针**。通过prev固定反转后链表头，通过head来反转，通过next指向剩下的链表头部

### 16.合并两个排序的链表

> 输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

思路：两个指针分别指向链表元素，然后比较两个元素大小，小的则连到合成后链表，直到达到一个链表的末尾。然后如果哪一个链表还有元素，直接连到合成后链表后面即可。

### 17.树的子结构(C)

>输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

思路：比较B是不是A的子树，B是不是A的右子树的子树，B是不是A的左子树的子树。如果根元素相同，则开始判断左子树和右子树

```javascript
function isSubtree(pRoot1,pRoot2){
    if (pRoot2 == null) return true;//pRoot2为null，表示子树已经遍历完
    if (pRoot1 == null) return false;
    if(pRoot1.val==pRoot2.val){
        return isSubtree(pRoot1.left,pRoot2.left) && isSubtree(pRoot1.right,pRoot2.right);
    }else{
        return false;
    }
}

function HasSubtree(pRoot1, pRoot2)
{
    if(pRoot1==null||pRoot2==null) return false;   
    return isSubtree(pRoot1,pRoot2)||HasSubtree(pRoot1.left,pRoot2)||HasSubtree(pRoot1.right,pRoot2);
}
```

### 18.二叉树镜像

> 操作给定的二叉树，将其变换为源二叉树的镜像

思路：先将根的**左右节点互换，然后就是递归调用**，对左右子树进行分别处理

### 19.顺时针打印矩阵(B)

> 输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

思路：

1. 选坐标为(0,0),(1,1)…的点记为(start,start)，作为开始坐标,下一圈开始坐标为(start+1,start+1)；
2. 关键：**判断是否进入下一圈(即是否打印完成)的条件**是`rows>start*2 && cols>start*2`；
3. 打印一圈的左上角坐标为(start,start)，右下角的坐标为(cols-start-1,rows-start-1)
4. 根据一圈左上角和右下角坐标判断“从左到右”，“从上到下”，“从右到左”，“从下到上”需要打印的点。

### 20.包含min函数的栈

> 定义栈的数据结构，请在该类型中实现一个能够得到栈最小元素的min函数。

思路：通过数组来实现栈的功能

```javascript
 result.map(val=>((min>val)?min=val:null));
```

### 21.栈的压入、弹出(C)

> 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列`1,2,3,4,5`是某栈的压入顺序，序列`4,5,3,2,1`是该压栈序列对应的一个弹出序列，但`4,3,5,1,2`就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

思路：设置辅助栈，压入元素，**判断栈顶元素是否等于出栈第一个，相同则出栈**，不同则继续入栈

### 22.从上往下打印二叉树

> 从上往下打印出二叉树的每个节点，同层节点从左至右打印。

思路：采用**队列**方法，先将**根节点放入队列**中，然后打印，接着**将左右节点加入队列**中。

```javascript
while (queue.length) {
  var temp = queue.shift();
  result.push(temp.val);
  if (temp.left) {
    queue.push(temp.left);
  }
  if (temp.right) {
    queue.push(temp.right);
  }
}
```

### 23.二叉搜索树的后序遍历(A)

>输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

思路：后序遍历最后一个元素为根节点，所以先遍历，将数组分为小于根节点部分和大于根节点部分，然后比较这两个部分是否符合与根节点的大小关系。

```javascript
function VerifySquenceOfBST(sequence)
{
    // write code here
    if(sequence.length<=0) return;
    return test(sequence,0,sequence.length-1)
}
function test(sequence,start,end){
    if(start>=end) return true;
    var i=end-1;
    while(i>=start && sequence[i]>sequence[end]){
        i--;
    }
    for(var j=i;j>=start;j--){
        if(sequence[j]>sequence[end]){
            return false;
        }
    }
    return test(sequence,start,i)&&test(sequence,i+1,end-1)
}
```

### 24.二叉树中和为某一值的路径(A)

>输入一颗二叉树和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

思路：判断当前结点是否是叶子结点，以及curtSum是否等于expectNumber。如果是，把当前路径保存在res结果中；

```javascript
function FindPath(root, expectNumber)
{
    var result=[];
    if(root==null) return result;
    dfs(root,0,[]);
    function dfs(root,current,path){
        current+=root.val;
        path.push(root.val)
        if(current==expectNumber && root.left==null && root.right ==null){
            result.push(path.slice(0))
        }
        if(root.left!=null){
            dfs(root.left,current,path)
        }
        if(root.right!=null){
            dfs(root.right,current,path)
        }
        path.pop()
    }
    return result;
}
```

### 25.复杂链表的复制

> 输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。

思路：将复杂链表分为**头结点和剩余结点**两部分，剩余部分采用递归方法。

```javascript
var node = new RandomListNode(pHead.label);
node.random = pHead.random;
node.next = Clone(pHead.next);
```

### 26.二叉搜索树与双向链表(A)

> 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

思路：

- 将左子树构成双向链表，返回的是左子树的尾结点，将其连接到root的左边； 
- 将右子树构成双向链表，将其追加到root结点之后，并返回尾结点；
- 向左遍历返回的链表至头结点处，即为所求双向链表的首结点。

```javascript
function Convert(pRootOfTree)
{
    // write code here
    if(pRootOfTree==null){
        return null;
    }
    var lastNode=null;
    lastNode=convertNode(pRootOfTree,lastNode);
    var head=lastNode;
    while(head && head.left){//循环到头部
        head=head.left;
    }
    return head;
}
function convertNode(root,lastNode){
    if(root==null) return;
    if(root.left){//左子树
        lastNode=convertNode(root.left,lastNode)
    }
    root.left=lastNode;
    if(lastNode){
        lastNode.right=root;
    }
    lastNode=root;
    if(root.right){//右子树
        lastNode=convertNode(root.right,lastNode)
    }
    return lastNode;
}
```

### 27.字符串排列（B）

> 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

思路：**使用递归方法，n个元素的全排列 = （n-1） 个元素全排列 + 一个元素作为前缀。**判断是否重复

```javascript
function sortString(arr,sortTemp,result){...}
```

### 28.数组中出现次数超过一半

>数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

思路：可以使用map数据结构

```javascript
numbers.map(x=>(countObj[x]?countObj[x]++:countObj[x]=1));
for(var i in countObj)
```

### 29.最小的k个数

> 输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

思路：先排序，然后slice()即可。

```javascript
input.sort((a,b)=>(a-b));
return input.slice(0,k);
```

### 30.连续子数组的最大和(C)

> 在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。你会不会被他忽悠住？(子向量的长度至少是1)

思路：**如果和为负数，则重新开始，如果和为正数，则继续加**，然后比较大小，选出最大和即可。

```javascript
var tempSum=0,sum=-Number.MAX_VALUE;
array.map(function(a){
    tempSum=(tempSum<0)?a:tempSum+a;
    sum=(sum<tempSum)?tempSum:sum;
})
```
### 31.整数中1出现的次数(C)

> 求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。求出任意非负整数区间中1出现的次数

思路1：

1. 如果第i位上的数字为0，则第i位可能出现1的次数由其高位决定，若没有高位，则视为0，此时第i位可能出现1的次数为：`其高位数*10^(i-1)`，例如若c为0，则次数为ab*10^2;
2. 如果第i位上的数字为1，则第i位上可能出现1的次数受其高位和低位影响，若没有，则视为0，此时第i位可能出现1的次数：`其高位数*10^(i-1)+（低位数+1）`，例如若c为1，则次数为ab*10^2+（de+1）;
3. 如果第i位上的数字大于1，则第i位上可能出现1的次数受其高位影响，若没有，则视为0，此时第i位可能出现1的次数：`（其高位数+1）*10^(i-1)`，例如若c大于1，则次数为（ab+1）*10^2;

思路2：暴力解决，一个数一个数判断

### 32.把数组排成最小的数(B)

> 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

思路：排序规则如下：

- 若ab > ba 则 a > b，
- 若ab < ba 则 a < b，
- 若ab = ba 则 a = b；

```javascript
numbers.sort(function(a,b){
        var s1 = a +''+b;
        var s2 = b+''+a;
        return parseInt(s1)-parseInt(s2);
    })
numbers.map(function(num){
        result = result.concat(num);
})
```

### 33.丑数

> 把只包含因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

思路：下一个丑数是由数组中**某个丑数A * 2，B * 3，C * 5中的最小值**得来的。**按序全部遍历**

```javascript
uglys[i]=Math.min(uglys[factor2]*2,uglys[factor3]*3,uglys[factor5]*5)
if(uglys[i]==uglys[factor2]*2) factor2++;
...
```

### 34.第一次只出现一次的字符

> 在一个字符串(1<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置

思路：使用map结构，将每次出现字符和出现次数放入map中

```javascript
strArr.map( s => (!count[s]?count[s]=1:count[s]++));
for(var i in count)
```

### 35.数组中的逆序对(A)

> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007 ; 注意：时间有限制

思路：利用**归并排序**思想，先求出每个组中逆序对数，然后合并、排序并统计，

### 36.两个链表的第一个公共结点(A)

>输入两个链表，找出它们的第一个公共结点。

思路：两个指针，遍历后比较结点的值

- 长度相同有公共结点，第一次就遍历到；没有公共结点，走到尾部NULL相遇，返回NULL
- 长度不同有公共结点，第一遍差值就出来了，第二遍一起到公共结点；没有公共，一起到结尾NULL。

```javascript
while(p1!=p2){
  p1=(p1==null?pHead2:p1.next)
  p2=(p2==null?pHead1:p2.next)
}
```

### 37.数字在排序数组中出现的次数

> 统计一个数字在排序数组中出现的次数。

思路:

1. 暴力解决方法，遍历
2. 二分查找方法：先看中间元素与k相比，小于则在左半部分，大于则在右半部分，

### 38.二叉树的深度

> 输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

思路：递归求左子树和右子树深度，然后比较，最终返回最大值加1

### 39.平衡二叉树(B)

>输入一棵二叉树，判断该二叉树是否是平衡二叉树。

思路：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。遍历左子树和右子树的深度，然后比较二者差值

### 40.数组中只出现一次的数字

> 一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

思路：将数组同去重后的数组进行比较，求出数组中元素出现的次数

### 41.和为S的连续正数序列(C)

> 小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? 

思路：设定两个指针，**如果和大于sum，左指针向后移位，如果小于，右指针向后移位**。 如果两个指针碰在一起，则跳出， **左指针一直小于sum的一半**

### 42.和为sum的两个数字

> 输入一个递增排序的数组和一个数字S，在数组中查找两个数，是的他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。 

思路：两头乘积最小，所以两个指针，一个从头，一个从尾分别遍历，当第一次出现和为S时，乘积最小。和41题本质相同

### 43.左旋转字符串

> 汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

思路：通过切分字符串，然后将后部分的字符串贴在前部分字符串前面

**注意：**

```javascript
var left=str.slice(0,n);
var right=str.slice(n);
```

### 44.翻转单词顺序序列

> 牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？

```javascript
//注意：join参数为分隔符
var array = str.split(' ');
return array.reverse().join(' ');
```

### 45.扑克牌顺子

> LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)…他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子…..LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何。为了方便起见,你可以认为大小王是0。

思路：首先先排序，然后统计大小王个数，然后遍历，看缺多少个数才能连续，如果超过大小王个数则失败。注意可能牌重复

### 46.圆圈中最后剩下的数(C)

> 每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0…m-1报数….这样下去….直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1)

思路：约瑟夫问题

1. 建数组
2. 模拟报数，将报数的人踢出数组，**注意要少1个序号**。
3. 直到数组只剩1个

> 注意：for循环中i为1开始

```javascript
for(var i=0;i<n;i++) arr.push(i);
var idx = 0;
while(arr.length > 1){
  for(var i=1;i<m;i++){
    idx=(idx+1)%arr.length;
  }
  arr.splice(idx,1);
}
return arr[0];
```

### 47.1+2+3+……+n

> 求1+2+3+…+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

思路：**短路判断+递归运算**

### 48. 不用加减乘除做加法(A)

> 写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

思路：

- 首先看十进制是如何做的： 5+7=12，三步走
- 第一步：**相加各位的值，不算进位**，得到2。
- 第二步：**计算进位值**，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。
- 第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。

```javascript
while(num2!=0){
  var temp = num1^num2;
  num2 = (num1&num2)<<1;
  num1 = temp;
}
return num1;
```

### 49.把字符串转换成整数(B)

> 将一个字符串转换成一个整数，要求不能使用字符串转换整数的库函数。

```javascript
//res=res*10+str[i]-'0';
res = res*10 + (arr[i] & 0xf);
```

### 50.数组中重复数字

> 在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

```javascript
//1.map
numbers.map(num=>(obj[num]?duplication.push(num):obj[num]=1));
duplication.shift();
//2.排序
numbers.sort((a,b)=>(a-b))
```

### 51.构建乘积数组(B)

> 给定一个数组A[0,1,…,n-1],请构建一个数组B[0,1,…,n-1],其中B中的元素`B[i]=A[0]*A[1]*…*A[i-1]*A[i+1]*…*A[n-1]`。不能使用除法。

思路：

- 构建**前向乘积数组**`C[i]=A[0]A[1]…*A[i-1]`，**即C[i]=C[i-1]*A[i-1]；**
- 构建**后向乘积数组**`D[i]=A[n-1]A[n-2]…A[n-i+1]`，**即D[i]=D[i+1]*A[i+1]；**
- 通过C[i],D[i]来求B[i]：**B[i]=C[i]*D[i]**

```javascript
for(var i=1;i<len;i++)
{
  arr[i]=arr[i-1]*array[i-1];
}
for(var i=len-2;i>=0;i--)
{
  temp*=array[i+1];
  arr[i]*=temp;
}
```



### 52.正则表达式匹配(A)

> 请实现一个函数用来匹配包括`’.’和’*‘`的正则表达式。模式中的字符’.’表示任意一个字符，而’*‘表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串”aaa”与模式”a.a”和”ab*ac*a”匹配，但是与”aa.a”和”ab*a”均不匹配

思路：

- 当模式中的第二个字符不是“*”时：

  1. 如果字符串第一个字符和模式中的第一个字符相匹配，那么字符串和模式都后移一个字符，然后匹配剩余的。
  2. 如果 字符串第一个字符和模式中的第一个字符相不匹配，直接返回false。

- 而当模式中的第二个字符是“*”时：

  如果字符串第一个字符跟模式第一个字符不匹配，则模式后移2个字符，继续匹配。如果字符串第一个字符跟模式第一个字符匹配，可以有3种匹配方式：

  1. 模式后移2字符，相当于x*被忽略；
  2. 字符串后移1字符，模式后移2字符；
  3. 字符串后移1字符，模式不变，即继续匹配字符下一位，因为*可以匹配多位；

### 53.表示数值的字符串(B)

> 请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串”+100”,”5e2”,”-123”,”3.1416”和”-1E-16”都表示数值。 但是”12e”,”1a3.14”,”1.2.3”,”+-5”和”12e+4.3”都不是。

```javascript
return s.match(/[\+\-]?[0-9]*(\.[0-9]*)?([Ee][\+-]?[0-9]+)?/g)[0] == s
```

### 54.字符流中第一个不重复的字符

> 请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符”go”时，第一个只出现一次的字符是”g”。当从该字符流中读出前六个字符“google”时，第一个只出现一次的字符是”l”。

思路：设置一个数组存放初次出现的元素。如果这个元素没有过，就放进数组；如果这个元素有过了，就把他从数组删除。输出数组的第一个元素。

### 55.链表中环的入口结点

> 一个链表中包含环，请找出该链表的环的入口结点。

思路：**通过map来存储每次访问的结点**，如果有重复，则是链表入口结点。

### 56.删除链表中重复的结点(B)

> 在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

思路：**需要三个指针**，**第一个指针first的next指向头结点，第二个指针pre指向first，确保每次指向的是重复结点的前一个结点，第三个指针head来遍历**，遇到重复结点，则向后，直到找到当前结点不等于下一个结点时，将pre的next指向head。

### 57.二叉树的下一个结点(B)

> 给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

思路：

1. 二叉树为空，则返回空；
2. **节点右孩子存在**，则设置一个指针从该节点的右孩子出发，一直沿着指向左子结点的指针找到的叶子节点即为下一个节点；
3. 节点不是根节点。如果该**节点是其父节点的左孩子**，则返回父节点；否则继续向上遍历其父节点的父节点，重复之前的判断，返回结果。

```javascript
function GetNext(pNode)
{
    // write code here
    if(pNode==null) return null;
    if(pNode.right!=null) {//节点右孩子存在情况
        pNode=pNode.right;
        while(pNode.left!=null){//沿着向左节点找到叶子节点
            pNode=pNode.left;
        }
        return pNode;
    }
    while(pNode.next!=null){
        var pRoot=pNode.next;
        if(pRoot.left==pNode){
            return pRoot
        }
        pNode=pNode.next;
    }
    return null

}
```

### 58.对称二叉树

>请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

思路：采用递归方法，先判断根节点的左子树和右子树是否为空，同时为空，则返回true，一个为空，则为false。同时不为空，则判断其值是否相等，然后递归判断左子树和右子树的

### 59.按之字形顺序打印二叉树(B)

>请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

思路：按深度遍历二叉树，深度（从0开始）为奇数的反转加入数组。

```javascript
function Print(pRoot)
{
    var queue = [];//存储节点队列
    var result = [];
    if(pRoot==null) return result;
    var nextLevel = 0;
    queue.push(pRoot);
    var toBePrinted = 1;//每一行节点个数
    var level = 0;//深度
    var arr = [];
    while(queue.length){
        var temp = queue.shift();
        toBePrinted--;
        arr.push(temp.val);
        if(temp.left){
            queue.push(temp.left);
            nextLevel++;
        }
        if(temp.right){
            queue.push(temp.right);
            nextLevel++;
        }
        if(toBePrinted==0){
            toBePrinted=nextLevel;
            nextLevel=0;
            level++;
            if(level%2==0){
                arr.reverse();
            }
            result.push(arr);
            arr = [];
        }
    }
    return result;
}
```

### 60.把二叉树打印成多行(B)

> 从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

思路：采用队列数据结构，同时需要两个变量分别存储这一层和下一层结点个数。当前层结点个数为零时，则输出结点

```javascript
function Print(pRoot)
{
    // write code here
    var queue = [];//存储节点队列
    var result = [];
    if(pRoot==null) return result;
    var nextLevel = 0;
    queue.push(pRoot);
    var toBePrinted = 1;//每一行节点个数
    var arr = [];
    while(queue.length){
        var temp = queue.shift();
        toBePrinted--;
        arr.push(temp.val);
        if(temp.left){
            queue.push(temp.left);
            nextLevel++;
        }
        if(temp.right){
            queue.push(temp.right);
            nextLevel++;
        }
        if(toBePrinted==0){
            toBePrinted=nextLevel;
            nextLevel=0;
            result.push(arr);
            arr = [];
        }
    }
    return result;
}
```

### 61.序列化二叉树(B)

>请实现两个函数，分别用来序列化和反序列化二叉树

思路：

- 序列化，将节点值存入数组中，空节点则使用特殊标记存入数组中。
- 反序列化，从数组中获取元素，为number类型则生成节点，为特殊标记，则为空节点

### 62.二叉搜索树的第k个结点(C)

> 给定一颗二叉搜索树，请找出其中的第k大的结点。例如， 5 / \ 3 7 /\ /\ 2 4 6 8 中，按结点数值大小顺序第三个结点的值为4。

思路：二叉搜索树，若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；任意节点的左、右子树也分别为二叉查找树； 
所以采用**中序遍历**的方法，遍历后的结果就是从小到大顺序的结果

### 63.数据流中的中位数

> 如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

思路：排序

### 64.滑动窗口的最大值

> 给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}

思路

1. 算出**滑动窗口个数=数组元素个数-滑动窗口大小+1**，然后**遍历循环，将队列头元素取出，存入新元素**。

2. 我的思路:

   ```javascript
   arr.push(Math.max.apply(Math,num.slice(i,i+size)));
   ```

### 65.矩阵中的路径(A)

> 请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。 例如 a b c e s f c s a d e e 矩阵中包含一条字符串”bcced”的路径，但是矩阵中不包含”abcb”路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

思路：**回溯算法**

这是一个可以用回朔法解决的典型题。首先，在矩阵中任选一个格子作为路径的起点。如果路径上的第i个字符不是ch，那么这个格子不可能处在路径上的第i个位置。如果路径上的第i个字符正好是ch，那么往相邻的格子寻找路径上的第i+1个字符。除在矩阵边界上的格子之外，其他格子都有4个相邻的格子。重复这个过程直到路径上的所有字符都在矩阵中找到相应的位置。

由于回朔法的**递归特性**，路径可以被**开成一个栈**。当在矩阵中定位了路径中前n个字符的位置之后，在与第n个字符对应的格子的周围都没有找到第n+1个字符，这个时候只要在路径上回到第n-1个字符，重新定位第n个字符。

由于路径不能重复进入矩阵的格子，**还需要定义和字符矩阵大小一样的布尔值矩阵**，用来标识路径是否已经进入每个格子。 当矩阵中坐标为（row,col）的格子和路径字符串中相应的字符一样时，从4个相邻的格子(row,col-1),(row-1,col),(row,col+1)以及(row+1,col)中去定位路径字符串中下一个字符
如果4个相邻的格子都没有匹配字符串中下一个的字符，表明当前路径字符串中字符在矩阵中的定位不正确，我们需要回到前一个，然后重新定位。

一直重复这个过程，直到路径字符串上所有字符都在矩阵中找到合适的位置

```javascript
function hasPath(matrix, rows, cols, path)
{
  function hasPathCore(matrix, rows, cols, row, col, path, pathIndex, visited) 	 {
    hasPath=hasPathCore(matrix, rows, cols, row - 1, col, path, pathIndex + 1, visited) ||
            hasPathCore(matrix, rows, cols, row + 1, col, path, pathIndex + 1, visited) ||
            hasPathCore(matrix, rows, cols, row, col - 1, path, pathIndex + 1, visited) ||
            hasPathCore(matrix, rows, cols, row, col + 1, path, pathIndex + 1, visited);
  }
}
```

### 66.机器人的运动范围(A)

> 地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

思路：**回溯算法**

```javascript
function movingCount(threshold, rows, cols)
{
  function movingCountSum(threshold,m,n,rows,cols,visited)
  {
    if(m>=0&&m<rows&&n>=0&&n<cols&&!visited[m][n]&&getSum(m,n)<=threshold)
    {
        visited[m][n]=true;
        count = 1+movingCountSum(threshold,m,n-1,rows,cols,visited)+
                movingCountSum(threshold,m,n+1,rows,cols,visited)+
                movingCountSum(threshold,m-1,n,rows,cols,visited)+
                movingCountSum(threshold,m+1,n,rows,cols,visited);
    }
  }
}
```



## 二刷

### 2.替换空格(B)

> \s+代表多个空格，？则表示取尽可能少的空格

### 4.重建二叉树(B)

### 11.二进制中1的个数(B)

### 12.数值的整数次方(B)

### 16.合并两个排序的链表(A)

```javascript
function Merge(pHead1, pHead2)
{
    if(pHead1==null) return pHead2;
    if(pHead2==null) return pHead1;

    if(pHead1.val <= pHead2.val)
    {
        pHead1.next = Merge(pHead1.next,pHead2);
        return pHead1;
    }
    else
    {
        pHead2.next = Merge(pHead1,pHead2.next);
        return pHead2;
    }
}
```

### 17.树的子结构(B)

> 输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

思路：比较B是不是A的子树，B是不是A的右子树的子树，B是不是A的左子树的子树。如果根元素相同，则开始判断左子树和右子树

> 注意：
>
> isSubtree中pRoot2要在pRoot1上面判断

```javascript
function isSubtree(pRoot1,pRoot2){
    if (pRoot2 == null) return true;//pRoot2为null，表示子树已经遍历完
    if (pRoot1 == null) return false;
    if(pRoot1.val==pRoot2.val){
        return isSubtree(pRoot1.left,pRoot2.left) && isSubtree(pRoot1.right,pRoot2.right);
    }else{
        return false;
    }
}

function HasSubtree(pRoot1, pRoot2)
{
    if(pRoot1==null||pRoot2==null) return false;   
    return isSubtree(pRoot1,pRoot2)||HasSubtree(pRoot1.left,pRoot2)||HasSubtree(pRoot1.right,pRoot2);
}
```

### 18.二叉树镜像(B)

```javascript
function Mirror(root)
{
    if(root == null) return;
    var left = Mirror(root.left);
    var right = Mirror(root.right);
	root.left = right;
    root.right = left;
    return root;
}
```

### 19.顺时针打印矩阵(?)

### 21.栈的压入、弹出(B)

思路：设置辅助栈，压入元素，**判断栈顶元素是否等于出栈第一个，相同则出栈**，不同则继续入栈

### 22.从上往下打印二叉树(B)

> 从上往下打印出二叉树的每个节点，同层节点从左至右打印。

思路：采用**队列**方法，先将**根节点放入队列**中，然后打印，接着**将左右节点加入队列**中。

```javascript
while (queue.length) {
  var temp = queue.shift();
  result.push(temp.val);
  if (temp.left) {
    queue.push(temp.left);
  }
  if (temp.right) {
    queue.push(temp.right);
  }
}
```

### 23.二叉搜索树的后序遍历(A)

### 24.二叉树中和为某一值的路径

### 25.复杂链表的复制(B)

### 26.二叉搜索树与双向链表(A)

### 27.字符串排列(A)

### 32.把数组排成最小的数(C)

### 35.数组中的逆序对(A)

### 56.删除链表中重复的结点(B)



