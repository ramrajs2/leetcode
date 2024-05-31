# Problem 
Link: https://leetcode.com/problems/kth-largest-element-in-an-array/description/

```
Given an integer array nums and an integer k, return the kth largest element in the array.

Note that it is the kth largest element in the sorted order, not the kth distinct element.

Can you solve it without sorting?

Example 1:

Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
Example 2:

Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

## Solution using Quick Select algorithm
Quick select is nothing but quick sort algorithm except a slight modification.

Quick Sort:
- chooses a pivot
- moves it to the position where it will be when the array is sorted
- recursively apply quick sort on left partition of pivot
- recursively apply quick sort on right partition of pivot

Quick Select:
- chooses a pivot
- moves it to the position where it will be when the array is sorted
- check if it is the index that we are looking for
  - if yes, return
  - else, choose to search in left partition or right partition

Note:
- In quick sort, both left partition and right partition are processed always. TC ranges from O(n logn) to O(n^2)
- In quick sort, we keep choosing only one of the partition (similar to binary search but no guarantee that both the partitions contain equal number of elements) - TC ranges from O(n) to O(n^2)

Check the video below to understand the algorithm better
- https://www.youtube.com/watch?v=ZAXSFph_L-A

Video above sorts in ascending order. For this problem, we need to sort by descending order. So conditions in the partition should be flipped accordingly.

## Code
```
class Solution {
    public int findKthLargest(int[] nums, int k) {
        if(nums == null || nums.length == 0 || k < 0 || k>nums.length) {
            return -1;
        }
        return findKthLargest(nums, 0, nums.length-1, k);
    }
    public int findKthLargest(int[] nums, int start, int end, int k) {
        int p = getPartition(nums, start, end);
        if(p == k-1) { // remember to check against k-1
            return nums[p];
        }
        if(k-1 < p) { // remember to check against k-1
            // move left
            return findKthLargest(nums, start, p-1, k);
        }
        else {
            // move right;
            return findKthLargest(nums, p+1, end, k); // Here, note that k is not reduced
        }
    }

    public int getPartition(int[] nums, int start, int end) {
        int l = start;
        int r = end;
        int pivot = nums[l];

        while(l<r) {
            if(nums[l+1] >= pivot) {
                swap(nums, l, l+1);
                l++;
            }
            else if(nums[r] < pivot) {
                r--;
            }
            else {
                swap(nums, l+1, r);
            }
        }
        return l;
    }

    private void swap(int[] nums, int p1, int p2) {
        // System.out.println("swap " + p1 + " & " + p2);
        int tmp = nums[p1];
        nums[p1] = nums[p2];
        nums[p2] = tmp;
    }
}
```
