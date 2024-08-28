# 代码随想录打卡第一天

题目：

数组的前三个题目，分别是二分法，移除元素，和有序数组的平方。



对于数组类型的题目，最重要的就是双指针方法。（广义来看滑动窗口也是双指针方法，理论上只要涉及到索引数组元素我们就可以看作有一个指针）



Q1:

二分法中

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums)
        while(left < right):
            mid = left + (right - left) // 2
            if nums[mid] > target:
                right = mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                return mid
        return -1
```

如视频强调的一样，我们一定要注意区间开闭（不然可能会造成死循环和代码逻辑不严谨，比如左闭右闭，右边的我们是可以取到的，那么在上面的判断中，nums[mid]和target的关系就已经确定了，不必再判断一次）



同时我们需要注意的是， 在双指针类型的题目，我们一般有一个while去做判断，判断的条件一般是左右指针的关系或者他们的区间取值。

在二分法中，我尝试去判断其他条件，比如nums[(left + right) // 2] != target，但这会导致很多问题，比如没有好的方法循环去算mid,同时下面还需要重复判断与target的大小关系，同时还要再判断左右指针的大小关系。相当于重复了很多代码，同时逻辑也更不清晰了，debug了很久写出来了一版正确的方法。但是不建议使用。



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
    def removeElement(self, nums: List[int], val: int) -> int:
        fast = 0
        slow = 0
        while(fast<len(nums)):
            if nums[fast] == val:
                fast += 1
                continue
            nums[slow] = nums[fast]
            slow += 1
            fast += 1
        return slow
```

同样要注意while判断的条件，本题目理解nums[slow] = nums[fast]是核心



Q3：

```
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        pointer1 = 0
        pointer2 = len(nums) - 1
        new_nums = [0] * len(nums)
        count = len(nums) - 1
        while pointer1 <= pointer2:
            if nums[pointer1]**2 <= nums[pointer2]**2:
                new_nums[count] = nums[pointer2]**2
                count -= 1
                pointer2 -= 1
            else:
                new_nums[count] = nums[pointer1]**2
                count -= 1
                pointer1 += 1
        # if pointer1 >= 0:
        #     new_nums[count] = nums[pointer1]**2
            
        # if pointer2 < len(nums):
        #     new_nums[count] = nums[pointer2]**2
            
        return new_nums
```

一开始处理的时候，是从前往后更新新的数组，但是这样只能从中间到两边去扩大判断。最后还要判断有没有完全走完数组，见注释的代码，同时对于[-5,-3,-2,-1]， 我们不能保证中间平方是最小的，所以我们要换一种思路：

两边要么是最大的，要么如上一大一小，所以我们从两边开始往中间走，同时从后往前倒序（双指针的重要思想之一，不仅可以从前往后，也可以从后往前！）添加从大到小的新数组。