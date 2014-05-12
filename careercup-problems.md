Amazon

给定大小为N的整数数组，每个元素的范围为[1, N]，求[1, N]内每个元素在数组中出现的次数。要求空间O(1)，时间O(n)。

从rand(5)获得rand(7)。

有N个车位，其中一个为空，要求通过最少步数把车的排序调整为指定序列。（约束：每辆车只能移动到空车位）
PAT1067 Sort with Swap

设计一个类似Paypal / Amazon的网站
i) Major components & the way they will interact. 
ii) Various way of scaling the web-site to support many users 
iii) Handling the failure cases like when the DB goes down, etc.

0/1二值的矩阵，求最长的连续的1（所有1均在同一行或同一列）。5452732613787648
大矩阵情况下如何处理。
The binary matrix is very big and it's stored in a file, with N = 2*10^6 lines, and M = 5000 characters each line. You cannot use more than 64 KB of main memory to store data. 
Find the longest sequence of 1's in column wise. 

5794059570380800
You are given a matrix where some pixels are white and some are black. Basically there are different disjoint images in the matrix. 
a) Expand/Shrink the images 
b) Count the no of images 
c) Color the images 
d) Rotate the images

判断点在三角形 / 多边形内

5104398418051072
Disconnect two nodes in a graph by removing minimum number of edges.


Google

Determine minimum sequence of adjacent values in the input parameter array that is greater than input parameter sum. 5653018213089280
Sliding Window

```
// Better
int main()
{
    const int Nmax = 10;
    int Arr[Nmax];

    int n = 6; //size of input Arr;
    Arr = {7, -3, 8, 4, 13,-5};
    int S = 15;

    int Sum = Arr[0];
    int wR = 0, wL = 0;
    int minSize = n+10;


    while(true){// make sure that Arr[wL] and Arr[wR] are positive.
        if (Sum<=0 and wR <n){
            wR++;
            wL = wR;
            Sum = Arr[wR];
            continue;
        };

        if (Sum <=S and wR<n){
            wR++;
            Sum += Arr[wR];
            continue;
        };

        if (Sum>S){
            minSize = min(minSize, wR-wL);
            Sum -= Arr[wL];wL++;

            while(Arr[wL] <=0 and wL < wR){
                Sum -= Arr[wL];
                wL++;
                if (Sum>S){
                    minSize = min(minSize, wR-wL);
                }
            };
        };
        if (wR>=n) break;
    };

    if (minSize>n) cout <<"No solution"<<endl;
    else cout <<"minimum sequence length is "<<minSize+1<<endl;

    return 0;

}

// Mine
int count(int nums[], int n, int target) {
    if (!nums || n <= 0) return INT_MAX;
    
    int result = INT_MAX;
    int counter = 0;
    int start = 0;
    int sum = 0;
    for (int i=0; i<n; ) {
        sum += nums[i];
        ++counter;
        if (sum >= target) {
            result = min(result, counter);
            while (sum >= target) {
                sum -= nums[start++];
                --counter;
            }
        } else {
            ++i;
        }
    }
    return result;
}
```

Given an array of (unsorted) integers, arrange them such that a < b > c < d > e... etc.
排序然后两两交换

Flatten an iterator of iterators in Java. If the input is [ [1,2], [3,[4,5]], 6], it should return [1,2,3,4,5,6]. Implement hasNext() and next(). Note that the inner iterator or list might be empty.
5898529851572224

同样是PAT1067的引申
The input is a sequence x1,x2,...,xn of integers in an arbitrary order, and another sequence 
a1,a2,..,an of distinct integers from 1 to n (namely a1,a2,...,an is a permutation of 
1, 2,..., n). Both sequences are given as arrays. Design an 0(n logn) algorithm to order 
the first sequence according to the order imposed by the permutation. In other words, for 
each i, Xi should appear in the position given in ai. For example, if x = 17, 5, 1,9, and a = 
3, 2, 4, 1, then the outcome should be x = 9, 5, 17, 1. The algorithm should be in-place, so 
you cannot use an additional array.

next_pemutation的应用
Find next higher number with same digits. 
Example 1 : if num = 25468, o/p = 25486 
Example 2 : if num = 21765, o/p = 25167 
Example 3 : If num = 54321, o/p = 54321 (cause it's not possible to gen a higher num than tiz with given digits ).

5678435150069760
Given the array of digits (0 is also allowed), what is the minimal sum of two integers that are made of the digits contained in the array. 
For example, array: 1 2 7 8 9. The min sum (129 + 78) should be 207

5988741646647296
无向图的三角形个数

LRU应用
Q: Design a component that will implement web browser history. the user goes to different site and once he press on history button you should display the last 5 (no duplicates allowed, and 5 can be any N later) if duplicates occur display the most recent one. so if user visit : G,A,B,C,A,Y and than press "history" we will display Y,A,C,B,G. and of course he can go later to two other websites and than press "history" we will show them than the previous 3. 
A: I solved it with stack, list and hash table in O(n) but it was too complicated and I didn't like my solution. please suggest something simpler.