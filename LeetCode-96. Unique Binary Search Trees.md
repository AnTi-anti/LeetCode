#### 题目描述（中等难度）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514144142576.jpg)
#### 解法一 递归
可以参考第[95题](https://blog.csdn.net/weixin_35770067/article/details/105888237)
对于这道题，我们会更简单些，只需要返回树的数量即可。求当前根的数量，只需要左子树的数量乘上右子树。
```java
public int numTrees(int n) { 
    if (n == 0) {
        return 0;
    }
    return getAns(1, n);

} 
private int getAns(int start, int end) { 
    int ans = 0;
    //此时没有数字，只有一个数字,返回 1
    if (start >= end) { 
        return 1;
    } 
    //尝试每个数字作为根节点
    for (int i = start; i <= end; i++) {
        //得到所有可能的左子树
        int leftTreesNum = getAns(start, i - 1);
        //得到所有可能的右子树
        int rightTreesNum  = getAns(i + 1, end);
        //左子树右子树两两组合
        ans+=leftTreesNum * rightTreesNum;
    }
    return ans;
}
```
我们甚至可以改写的更简单些。因为 95 题要把每颗树返回，所有传的参数是 start 和 end。这里的话，我们只关心数量，所以不需要具体的范围，而是传树的节点的数量即可。

```java
public int numTrees(int n) { 
    if (n == 0) {
        return 0;
    }
    return getAns(n);

}

private int getAns(int n) { 
    int ans = 0;
    //此时没有数字或者只有一个数字,返回 1
    if (n==0 ||n==1) { 
        return 1;
    } 
    //尝试每个数字作为根节点
    for (int i = 1; i <= n; i++) {
        //得到所有可能的左子树
        // i - 1 代表左子树节点的数量
        int leftTreesNum = getAns(i-1);
        //得到所有可能的右子树
        //n - i 代表左子树节点的数量
        int rightTreesNum  = getAns(n-i);
        //左子树右子树两两组合
        ans+=leftTreesNum * rightTreesNum;
    }
    return ans;
}
```
然后，由于递归的分叉，所以会导致很多重复解的计算，所以使用 memoization 技术，把递归过程中求出的解保存起来，第二次需要的时候直接拿即可。

```java
public int numTrees(int n) { 
    if (n == 0) {
        return 0;
    }
    HashMap<Integer,Integer> memoization = new HashMap<>();
    return getAns(n,memoization);

}

private int getAns(int n, HashMap<Integer,Integer> memoization) { 
    if(memoization.containsKey(n)){
        return memoization.get(n);
    }
    int ans = 0;
    //此时没有数字，只有一个数字,返回 1
    if (n==0 ||n==1) { 
        return 1;
    } 
    //尝试每个数字作为根节点
    for (int i = 1; i <= n; i++) {
        //得到所有可能的左子树
        int leftTreesNum = getAns(i-1,memoization);
        //得到所有可能的右子树
        int rightTreesNum  = getAns(n-i,memoization);
        //左子树右子树两两组合
        ans+=leftTreesNum * rightTreesNum;
    }
    memoization.put(n, ans);
    return ans;
}
```
#### 解法二 动态规划
假设有n个节点存在二叉排序树的个数是$G(n)$,令$f(i)$为以$i$为根的二叉搜索树的个数，则
$$G(n)=f(1)+f(2)+f(3)+f(4)+\ldots+f(n)$$
当$i$为根节点时，其左子树节点个数为$i-1$个，右子树节点为$n-i$,则$$f(i)=G(i-1) * G(n-i)$$
综合这两个公式可以得到卡特兰数公式
$$G(n)=G(0) * G(n-1)+G(1) *(n-2)+\ldots+G(n-1) * G(0)$$

画个图简单理解下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514150136146.jpg#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514150200470.jpg#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514150226963.jpg#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514150256325.jpg#pic_center)
代码如下：

```java
public int numTrees(int n) {
		int[]dp=new int[n+1];
		dp[0]=1;
		dp[1]=1;
		
		for(int i=2;i<n+1;i++) {
			for(int j=1;j<i+1;j++) {
				dp[i]=dp[i]+dp[j-1]*dp[i-j];
			}
		}
		return dp[n];
	}
```
#### 参考文档
1.https://zhuanlan.zhihu.com/p/71614339
2.[不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/solution/bu-tong-de-er-cha-sou-suo-shu-by-leetcode/)
