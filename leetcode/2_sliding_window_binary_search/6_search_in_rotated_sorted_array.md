Input: nums = [4,5,6,7,0,1,2], target = 0

Iteration 1:
  left=0, right=6, mid=3
  nums[mid]=7
  
  Check which half is sorted:
  nums[0]=4 <= nums[3]=7? YES
  → Left half [4,5,6,7] is sorted
  
  Is target in sorted left half?
  4 <= 0 < 7? NO
  → Target must be in right half
  left = 4

Iteration 2:
  left=4, right=6, mid=5
  nums[mid]=1
  
  Check which half is sorted:
  nums[4]=0 <= nums[5]=1? YES
  → Left half [0,1] is sorted
  
  Is target in sorted left half?
  0 <= 0 < 1? YES
  → Target is in left half
  right = 4

Iteration 3:
  left=4, right=4, mid=4
  nums[mid]=0
  
  nums[4] == target? YES! ✓
  
Output: 4
```

---

## 🎯 Visual Explanation
```
Array: [4,5,6,7,0,1,2]
         ↑     ↑     ↑
       left   mid  right

Pivot is between 7 and 0 (rotation point)

At mid=3:
Left half:  [4,5,6,7] ← sorted!
Right half: [0,1,2]   ← sorted!

One half is ALWAYS sorted!
