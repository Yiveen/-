# 代码随想录打卡第二天



Q1:

滑动窗口

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        fast = 0
        slow = 0
        sum_number = 0
        min_len = float('inf')
        while (fast < len(nums)):
            sum_number += nums[fast]

            while sum_number >= target:
                min_len = min(min_len, fast-slow+1)
                sum_number -= nums[slow]
                slow += 1

            fast += 1

        return min_len if min_len != float('inf') else 0
```

滑动窗口本质上还是一个双指针问题， 滑动窗口实际上是双层遍历的优化版本，而双指针其实只有一层遍历，只不过是从头尾开始遍历的。



注意这道题目要求的是‘所有的符合条件的，大于等于的，最短长度’，所以我们要完全遍历才可以，但双层遍历非常暴力，使用双指针省去了很多没必要的遍历。

                sum_number -= nums[slow]
                slow += 1

这一段顺序很重要，不然我们就不是先减去这个窗口中现在最前的元素了，而是减去下一个元素了

对于while循环的判断，因为fast肯定移动最快，所以只需要判断fast小于数组长度即可，不需要再判断比如slow<-fast(也可以判断，但是没必要)



```
class Solution:
    def search(self, nums: List[int], target: int) -> int:
            left = 0
            right = len(nums) - 1

            mid = (left + right) // 2
            if nums[left] == target:
                return left
            elif nums[right] == target:
                return right

            while (nums[(left + right) // 2] != target):
                mid = (left + right)// 2
                if nums[(left + right)// 2] < target:
                    left = mid + 1
                else:
                    right = mid - 1
                if left >= right:
                    break
            if nums[(left + right) // 2] == target:
                return (left + right) // 2
            else:
                return -1
```

在区间确定之后，我们需要有两个点注意:1. 更新left和right判断是不是要+/-1。 2. while判断left和right的小于或者小于等于的判断。 一个没注意好都会导致可能的死循环。



Q2：

```
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        count = 1 #从1开始
        loop, mid = n//2, n//2 
        nums = [[0] * n for _ in range(n)]
        startx, starty = 0, 0
        for offset in range(1, loop+1):
            for i in range(starty, n - offset):
                nums[startx][i] = count
                count += 1
            for i in range(startx, n - offset):
                nums[i][n - offset] = count
                count += 1
            for i in range(n - offset, starty, -1):
                nums[n - offset][i] = count
                count += 1
            for i in range(n - offset, startx, -1):
                nums[i][starty] = count
                count += 1
            startx += 1
            starty += 1
        if n % 2!= 0:
            nums[mid][mid] = count
        return nums
```

螺旋数组要判断的内容很多，所以一定要维持不变量。注意判断即可。

我们首先要确定终止条件，这里我们选用的是loop，根据我们的分析，不同的n会有不同的loop，所以loop达到之后我们就不再for循环，但是奇数会剩一个数组，这里我们单独处理。



python定义二维数组:

nums = [[0] * n for _ in range(n)]



再一个注意数组元素索引，这里我们新建startx，starty，loop的关系（判断loop决定了offset不会越界），同时去迭代的更新startx，starty等



for i in range(n - offset, startx, -1)逆序索引的话，我们是range(大， 小， -1)



Q3：

```
import sys
input = sys.stdin.read

# def sum_array(nums, n, left, right):
#     sums = 0
#     for i in range(left, right+1):
#         sums += nums[i]
#     return sums
    
# n = int(input())
# nums = []
# for i in range(n):
#     nums.append(int(input()))

def main():
    data = input().split()
    index = 0
    n = int(data[index])
    
    nums = []
    
    for i in range(n):
        nums.append(int(data[i+1]))
        
    index += n + 1
    
    p = [0] * n 
    psum = 0
    
    for i in range(n):
        psum += nums[i]
        p[i] = psum

    results = []
    while index < len(data):
        left = int(data[index])
        right = int(data[index + 1])
        index += 2
        
        if left == 0:
            num_sum = p[right] - 0
        else:
            num_sum = p[right] - p[left - 1]
            
        results.append(num_sum)

    for result in results:
        print(result)
            
    return num_sum
        

if __name__ == "__main__":
    main()
    
```

这个题目一方面是学习如何处理ACM格式的输入，另一方面是如何高效的处理题目中多次的输入查询。

ACM的处理是：注意一定都要转换为整数格式   .split会把空格和分行都分开，所以不能只是处理行，基本就是一个data，一个index就处理掉了
    

    data = input().split()
    index = 0
    n = int(data[index])
    
    nums = []
    
    for i in range(n):
        nums.append(int(data[i+1]))
        
    index += n + 1

高效处理的方法是先求和，连续范围的话就是两个sum的区间作差，但是要注意比如[2,5]应该是p5-p1，同时p1不能越界。