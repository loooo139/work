# 剑指offer 51 数组中的逆序对

## 题目描述：

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

对于这类数组中找数字之间关系的题目，一种解题思路就是拆分数组来解决子问题，把大问题拆成小问题，把小问题一一解决，大问题的答案也就出来了。

[fun4LeetCode帖子](https://discuss.leetcode.com/topic/79227/general-principles-behind-problems-similar-to-reverse-pairs)详细讲解了两种方法去解决这类问题。

### 1 顺序重现关系 

**T(i, j) = T(i, j - 1) + C**这里的c就是处理最后一个数字的子问题。用文字描述就是已知逆序对的第二个数字是nums[j]，在子数组中**nums[i,j-1]**中找到满足条件（nums[k]>nums[j]）条件的逆序对第一个数字。根据这个顺序，逆序的条件自然是满足的，只需要满足大小条件就好了。在子数组中寻找符合条件的数字。最简单的方法就是线性扫描，显然这种方法的时间复杂度过高O(n**2)。简化一点的方法可以使用二分查找来做。

```cpp
int reversepairs(vector<int>nums){
    if(nums.size()<=1)return 0;
    int res=0;
    vector<int>t;
    for(int i=nums.size()-1;i>=0;i--){
        int d=distance(t.begin(),lower_bound(t.begin(),t.end(),nums[i]/2.0));
        res+=d;
        t.insert(lower_bound(t.begin(),t.end(),nums[i]),nums[i]);
    }
    return res;
}
```

问题再次出现由于这个题目要求逆序数，第一个数字要>=2倍的第一个数，上文的方法中要两次寻找位置，第一次寻找num[j]/2.0的位置，第二次寻找nums[j]的位置。造成了耗时。

再次分析这个问题，我们可以看出来问题分为两步，第一步逆序，第二部满足大小条件。当我们逆序遍历数组时，逆序的条件就会自动满足的。这题的解答是使用树状数组BIT。之前的刷题中也遇到了BIT其主要特点就是可以logn

的时间下求和，奇数index存放数字，偶数index存放若干数字的和。这里的改进在于以数字的位置为index存放进BIT，而BIT中存放的数字出现的次数。

首先我们对数组排序得到sortArray，然后记录下每个数字在有序数组中最后出现的位置，然后我们逆序遍历数组比如nums[j]，我们在sortArray中寻找满足不逆序对条件(>=nums[j]/2.0)的第一个数的位置比如 k，则在k-1-0位置上的数字均为满足大小条件的数字。而这个位置就是BIT的index，BIT中存放值是该数字出现的次数，对其求和就可以得到逆序对中第一个是nums[j]的所有数字。

```cpp
class Solution {
public:
 int reversePairs(vector<int>nums){
        if(nums.size()<=1)return 0;
        int n=nums.size();
        vector<int>BIT(n+1);
        vector<int>sortArray=nums;
        sort(sortArray.begin(),sortArray.end());
        map<int,int>m;
        for(int i=0;i<n;i++)
            m[sortArray[i]]=i+1;
        int res=0;
        for(int i=n-1;i>=0;i--){
            int index=lower_bound(sortArray.begin(),sortArray.end(),nums[i]/2.0)-sortArray.begin();
            res+=getSum(index,BIT);
            update(m[nums[i]],BIT);
        }
        return res;
    }
    int getSum(int index,vector<int>&BIT){
        int t=index,res=0;
        while(t>0){
            res+=BIT[t];
            t-=t&-t;
        }
        return res;
    }
    void update(int i,vector<int>&BIT){
        while(i<BIT.size()){
            BIT[i]+=1;
            i+=i&-i;
        }
    }
};
```

----

## 2 分割重现关系

用式子表示是T(i, j) = T(i, m) + T(m+1, j) + C。这里的C就是处理合并两个部分的子问题，那么用文字来描述就是“已知翻转对的两个数字分别在子数组nums[i, m]和nums[m+1, j]之中，求满足要求的翻转对的个数”，

这里翻转对的两个条件中的顺序条件已经满足，就只需要找到满足大小关系的的数对即可。这里两个数字都是不确定的，如果用暴力搜索肯定会被OJ唾弃。但是如果两个子数组是有序的，那么我们可以用双指针的方法在线性时间内就可以统计出符合题意的翻转对的个数。要想办法产生有序的子数组，那么这就和MergeSort的核心思想完美匹配了。我们知道混合排序就是不断的将数组对半拆分成子数组，拆到最小的数组后开始排序，然后一层一层的返回，最后原数组也是有序的了。这里我们在混合排序的递归函数中，对于有序的两个子数组进行统计翻转对的个数，然后再逐层返回，这就完美的实现了上述的分割重现关系的思想。整个的写法非常的简洁，实在是太叼了。博主的直觉表明，fun4LeetCode大神肯定是国人，不要问我为什么，因为这么强的肯定是中国人，哈～

```cpp
class Solution{
public:
    int reversePairs(vector<int>&nums){
        return mergeSort(nums,0,nums.size()-1);
    }
    int mergeSort(vector<int>&nums,int left,int right){
        if(left>=right)return 0;
        int mid=left+(right-left)/2;
        int res=mergeSort(nums,left,mid)+mergerSort(nums,mid+1,right);
        for(int i=left,j=mid+1;i<=mid;i++){
            while(j<=right&&nums[i]/2.0>nums[j])j++;
            res+=j-(mid+1);
        }
        sort(nums.begin()+left,nums.begin()+right+1);
        return res;
    }
}

```

上面的代码问题就是排序部分没结合。修改如下：

```cpp
class Solution{
public:
    int reversePairs(vector<int>&nums){
        return mergeSort(nums,0,nums.size()-1);
    }
    int mergeSort(vector<int>&nums,int left,int right){
        if(left>=right)return 0;
        int mid=left+(right-left)/2;
        int res=mergeSort(nums,left,mid)+mergeSort(nums,mid+1,right);
        int temp[right-left+1];
        int i=left,j=mid+1,k=0,h=mid+1;
        while(i<=mid){
            while(j<=right&&nums[i]/2.0>nums[j])j++;
            res+=j-(mid+1);//j 记录下满足逆序的位置
            while(h<=right&&nums[i]>=nums[h])temp[k++]=nums[h++];
            temp[k++]=nums[i++];//k记录排序的位置，h记录下右半部分已排序的位置。
        }
        while(h<=right)temp[k++]=nums[h++];
        for(int i=left;i<=right;i++)
            nums[i]=temp[i-left];
        return res;
    }
};
```



It looks like a host of solutions are out there (`BST`-based, `BIT`-based, `Merge-sort`-based). Here I'd like to focus on the general principles behind these solutions and its possible application to a number of similar problems.



The fundamental idea is very simple: **break down the array and solve for the subproblems**.



A breakdown of an array naturally reminds us of subarrays. To smoothen our following discussion, let's assume the input array is `nums`, with a total of `n` elements. Let `nums[i, j]` denote the subarray starting from index `i` to index `j` (both inclusive), `T(i, j)` as the same problem applied to this subarray (for example, for [**Reverse Pairs**](https://leetcode.com/problems/reverse-pairs/?tab=Description), `T(i, j)` will represent the total number of important reverse pairs for subarray `nums[i, j]`).



With the definition above, it's straightforward to identify our original problem as `T(0, n - 1)`. Now the key point is how to construct solutions to the original problem from its subproblems. This is essentially equivalent to building recurrence relations for `T(i, j)`. Since if we can find solutions to `T(i, j)` from its subproblems, we surely can build solutions to larger subarrays until eventually the whole array is spanned.



While there may be many ways for establishing recurrence relations for `T(i, j)`, here I will only introduce the following two common ones:



1. 

   `T(i, j) = T(i, j - 1) + C`, i.e., elements will be processed sequentially and `C` denotes the subproblem for processing the last element of subarray `nums[i, j]`. We will call this sequential recurrence relation.

   

2. 

   `T(i, j) = T(i, m) + T(m + 1, j) + C` where `m = (i+j)/2`, i.e., subarray `nums[i, j]` will be further partitioned into two parts and `C` denotes the subproblem for combining the two parts. We will call this partition recurrence relation.

   



For either case, the nature of the subproblem `C` will depend on the problem under consideration, and it will determine the overall time complexity of the original problem. So usually it's crucial to find efficient algorithm for solving this subproblem in order to have better time performance. Also pay attention to possibilities of overlapping subproblems, in which case a dynamic programming (DP) approach would be preferred.



Next, I will apply these two recurrence relations to this problem "Reverse Pairs" and list some solutions for your reference.



------



**I -- Sequential recurrence relation**



Again we assume the input array is `nums` with `n` elements and `T(i, j)` denotes the total number of important reverse pairs for subarray `nums[i, j]`. For sequential recurrence relation, we can set `i = 0`, i.e., the subarray always starts from the beginning. Therefore we end up with:



```
T(0, j) = T(0, j - 1) + C
```



where the subproblem `C` now becomes **"find the number of important reverse pairs with the first element of the pair coming from subarray nums[0, j - 1]while the second element of the pair being nums[j]"**.



Note that for a pair `(p, q)` to be an important reverse pair, it has to satisfy the following two conditions:



1. `p < q`: the first element must come before the second element;
2. `nums[p] > 2 * nums[q]`: the first element has to be greater than twice of the second element.



For subproblem `C`, the first condition is met automatically; so we only need to consider the second condition, which is equivalent to searching for all elements within subarray `nums[0, j - 1]` that are greater than twice of `nums[j]`.



The straightforward way of searching would be a linear scan of the subarray, which runs at the order of `O(j)`. From the sequential recurrence relation, this leads to the naive `O(n^2)` solution.



To improve the searching efficiency, a key observation is that the order of elements in the subarray does not matter, since we are only interested in the total number of important reverse pairs. This suggests we may sort those elements and do a binary search instead of a plain linear scan.



If the searching space (formed by elements over which the search will be done) is "static" (it does not vary from run to run), placing the elements into an array would be perfect for us to do the binary search. However, this is not the case here. After the `j-th` element is processed, we need to add it to the searching space so that it becomes searchable for later elements, which renders the searching space expanding as more and more elements are processed.



Therefore we'd like to strike a balance between searching and insertion operations. This is where data structures like binary search tree (`BST`) or binary indexed tree (`BIT`) prevail, which offers relatively fast performance for both operations.



**1. BST-based solution**



we will define the tree node as follows, where `val` is the node value and `cnt` is the total number of elements in the subtree rooted at current node that are greater than or equal to `val`:



```
class Node {
    int val, cnt;
    Node left, right;
        
    Node(int val) {
        this.val = val;
        this.cnt = 1;
    }
}
```



The searching and insertion operations can be done as follows:



```
private int search(Node root, long val) {
    if (root == null) {
    	return 0;
    } else if (val == root.val) {
    	return root.cnt;
    } else if (val < root.val) {
    	return root.cnt + search(root.left, val);
    } else {
    	return search(root.right, val);
    }
}

private Node insert(Node root, int val) {
    if (root == null) {
        root = new Node(val);
    } else if (val == root.val) {
        root.cnt++;
    } else if (val < root.val) {
        root.left = insert(root.left, val);
    } else {
        root.cnt++;
        root.right = insert(root.right, val);
    }
    
    return root;
}
```



And finally the main program, in which we will search for all elements no less than twice of current element plus `1` (converted to `long` type to avoid overflow) while insert the element itself into the BST.



**Note:** this homemade BST is not self-balanced and the time complexity can go as bad as `O(n^2)` (in fact you will get `TLE` if you copy and paste the solution here). To guarantee `O(nlogn)` performance, use one of the self-balanced BST's (e.g. `Red-black` tree, `AVL` tree, etc.).



```
public int reversePairs(int[] nums) {
    int res = 0;
    Node root = null;
    	
    for (int ele : nums) {
        res += search(root, 2L * ele + 1);
        root = insert(root, ele);
    }
    
    return res;
}
```



**2. BIT-based solution**



For `BIT`, the searching and insertion operations are:



```java
private int search(int[] bit, int i) {
    int sum = 0;
    
    while (i < bit.length) {
        sum += bit[i];
        i += i & -i;
    }

    return sum;
}

private void insert(int[] bit, int i) {
    while (i > 0) {
        bit[i] += 1;
        i -= i & -i;
    }
}
```



And the main program, where again we will search for all elements greater than twice of current element while insert the element itself into the BIT. For each element, the `"index"` function will return its index in the `BIT`. Unlike the BST-based solution, this is guaranteed to run at `O(nlogn)`.



```
public int reversePairs(int[] nums) {
    int res = 0;
    int[] copy = Arrays.copyOf(nums, nums.length);
    int[] bit = new int[copy.length + 1];
    
    Arrays.sort(copy);
    
    for (int ele : nums) {
        res += search(bit, index(copy, 2L * ele + 1));
        insert(bit, index(copy, ele));
    }
    
    return res;
}

private int index(int[] arr, long val) {
    int l = 0, r = arr.length - 1, m = 0;
    	
    while (l <= r) {
    	m = l + ((r - l) >> 1);
    		
    	if (arr[m] >= val) {
    	    r = m - 1;
    	} else {
    	    l = m + 1;
    	}
    }
    
    return l + 1;
}
```



More explanation for the BIT-based solution:



1. 

   We want the elements to be sorted so there is a sorted version of the input array which is `copy`.

   

2. 

   The `bit` is built upon this sorted array. Its length is one greater than that of the `copy` array to account for the root.

   

3. 

   Initially the `bit` is empty and we start doing a sequential scan of the input array. For each element being scanned, we first search the `bit` to find all elements greater than twice of it and add the result to `res`. We then insert the element itself into the `bit` for future search.

   

4. 

   Note that conventionally searching of the `bit` involves traversing towards the root from some index of the `bit`, which will yield a predefined running total of the `copy` array up to the corresponding index. For insertion, the traversing direction will be opposite and go from some index towards the end of the `bit` array.

   

5. 

   For each scanned element of the input array, its searching index will be given by the index of the first element in the `copy` array that is greater than twice of it (shifted up by `1` to account for the root), while its insertion index will be the index of the first element in the `copy` array that is no less than itself (again shifted up by `1`). This is what the `index` function is for.

   

6. 

   For our case, the running total is simply the number of elements encountered during the traversal process. If we stick to the convention above, the running total will be the number of elements smaller than the one at the given index, since the `copy` array is sorted in ascending order. However, we'd actually like to find the number of elements greater than some value (i.e., twice of the element being scanned), therefore we need to flip the convention. This is what you see inside the `search` and `insert` functions: the former traversing towards the end of the `bit` while the latter towards the root.

   

------



**II -- Partition recurrence relation**



For partition recurrence relation, setting `i = 0, j = n - 1, m = (n-1)/2`, we have:



```
T(0, n - 1) = T(0, m) + T(m + 1, n - 1) + C
```



where the subproblem `C` now reads **"find the number of important reverse pairs with the first element of the pair coming from the left subarray nums[0, m]while the second element of the pair coming from the right subarray nums[m + 1, n - 1]"**.



Again for this subproblem, the first of the two aforementioned conditions is met automatically. As for the second condition, we have as usual this plain linear scan algorithm, applied for each element in the left (or right) subarray. This, to no surprise, leads to the `O(n^2)` naive solution.



Fortunately the observation holds true here that the order of elements in the left or right subarray does not matter, which prompts sorting of elements in both subarrays. With both subarrays sorted, the number of important reverse pairs can be found in linear time by employing the so-called two-pointer technique: one pointing to elements in the left subarray while the other to those in the right subarray and both pointers will go only in one direction due to the ordering of the elements.



The last question is which algorithm is best here to sort the subarrays. Since we need to partition the array into halves anyway, it is most natural to adapt it into a `Merge-sort`. Another point in favor of `Merge-sort` is that the searching process above can be embedded seamlessly into its merging stage.



So here is the `Merge-sort`-based solution, where the function `"reversePairsSub"` will return the total number of important reverse pairs within subarray `nums[l, r]`. The two-pointer searching process is represented by the nested `while` loop involving variable `p`, while the rest is the standard merging algorithm.



```
public int reversePairs(int[] nums) {
    return reversePairsSub(nums, 0, nums.length - 1);
}
    
private int reversePairsSub(int[] nums, int l, int r) {
    if (l >= r) return 0;
        
    int m = l + ((r - l) >> 1);
    int res = reversePairsSub(nums, l, m) + reversePairsSub(nums, m + 1, r);
        
    int i = l, j = m + 1, k = 0, p = m + 1;
    int[] merge = new int[r - l + 1];
        
    while (i <= m) {
        while (p <= r && nums[i] > 2 L * nums[p]) p++;
        res += p - (m + 1);
				
        while (j <= r && nums[i] >= nums[j]) merge[k++] = nums[j++];
        merge[k++] = nums[i++];
    }
        
    while (j <= r) merge[k++] = nums[j++];
        
    System.arraycopy(merge, 0, nums, l, merge.length);
        
    return res;
}
```

------



**III -- Summary**



Many problems involving arrays can be solved by breaking down the problem into subproblems applied on subarrays and then link the solution to the original problem with those of the subproblems, to which we have sequential recurrence relation and partition recurrence relation. For either case, it's crucial to identify the subproblem `C` and find efficient algorithm for approaching it.



If the subproblem `C` involves searching on "dynamic searching space", try to consider data structures that support relatively fast operations on both searching and updating (such as `self-balanced BST`, `BIT`, `Segment tree`, `...`).



If the subproblem `C` of partition recurrence relation involves sorting, `Merge-sort` would be a nice sorting algorithm to use. Also, the code could be made more elegant if the solution to the subproblem can be embedded into the merging process.



If there are overlapping among the subproblems `T(i, j)`, it's preferable to cache the intermediate results for future lookup.



Lastly let me name a few leetcode problems that fall into the patterns described above and thus can be solved with similar ideas.



[315. Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)
[327. Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/)



For `leetcode 315`, applying the sequential recurrence relation (with `j` fixed), the subproblem `C` reads: **find the number of elements out of visited ones that are smaller than current element**, which involves searching on "dynamic searching space"; applying the partition recurrence relation, we have a subproblem `C`: **for each element in the left half, find the number of elements in the right half that are smaller than it**, which can be embedded into the merging process by noting that these elements are exactly those swapped to its left during the merging process.



For `leetcode 327`, applying the sequential recurrence relation (with `j` fixed) on the pre-sum array, the subproblem `C` reads: **find the number of elements out of visited ones that are within the given range**, which again involves searching on "dynamic searching space"; applying the partition recurrence relation, we have a subproblem `C`: **for each element in the left half, find the number of elements in the right half that are within the given range**, which can be embedded into the merging process using the two-pointer technique.



Anyway, hope these ideas can sharpen your skills for solving array-related problems.