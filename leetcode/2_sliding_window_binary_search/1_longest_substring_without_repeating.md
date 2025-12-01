## 🔍 Step-by-Step Execution

Let me trace through: `s = "abcabcbb"`

---

## Initial State

```rust
s = "abcabcbb"
chars = ['a', 'b', 'c', 'a', 'b', 'c', 'b', 'b']
         0    1    2    3    4    5    6    7

left = 0
set = {}
max_area = 0
```

---

## Iteration 1: right = 0, char = 'a'

```rust
// Check: is 'a' in set? NO
// Skip while loop

// Add 'a' to set
set = {'a'}

// Update max_area
window size = 0 - 0 + 1 = 1
max_area = max(0, 1) = 1

State:
  Window: "a" [left=0, right=0]
  set = {'a'}
  max_area = 1
```

---

## Iteration 2: right = 1, char = 'b'

```rust
// Check: is 'b' in set? NO
// Skip while loop

// Add 'b' to set
set = {'a', 'b'}

// Update max_area
window size = 1 - 0 + 1 = 2
max_area = max(1, 2) = 2

State:
  Window: "ab" [left=0, right=1]
  set = {'a', 'b'}
  max_area = 2
```

---

## Iteration 3: right = 2, char = 'c'

```rust
// Check: is 'c' in set? NO
// Skip while loop

// Add 'c' to set
set = {'a', 'b', 'c'}

// Update max_area
window size = 2 - 0 + 1 = 3
max_area = max(2, 3) = 3

State:
  Window: "abc" [left=0, right=2]
  set = {'a', 'b', 'c'}
  max_area = 3
```

---

## Iteration 4: right = 3, char = 'a' ⚠️ DUPLICATE!

```rust
// Check: is 'a' in set? YES! 🚨
// Enter while loop to shrink window

While loop iteration 1:
  // Remove chars[left] = 'a'
  set.remove('a')
  set = {'b', 'c'}
  left = 1
  
  // Check again: is 'a' in set? NO
  // Exit while loop

// Now add 'a' to set
set = {'b', 'c', 'a'}

// Update max_area
window size = 3 - 1 + 1 = 3
max_area = max(3, 3) = 3

State:
  Window: "bca" [left=1, right=3]
  set = {'b', 'c', 'a'}
  max_area = 3
```

**What happened?**
- Found duplicate 'a'
- Removed 'a' from left side
- Window shifted: ~~"abc"~~a → "bca"

---

## Iteration 5: right = 4, char = 'b' ⚠️ DUPLICATE!

```rust
// Check: is 'b' in set? YES! 🚨
// Enter while loop

While loop iteration 1:
  // Remove chars[1] = 'b'
  set.remove('b')
  set = {'c', 'a'}
  left = 2
  
  // Check: is 'b' in set? NO
  // Exit while loop

// Add 'b' to set
set = {'c', 'a', 'b'}

// Update max_area
window size = 4 - 2 + 1 = 3
max_area = max(3, 3) = 3

State:
  Window: "cab" [left=2, right=4]
  set = {'c', 'a', 'b'}
  max_area = 3
```

**What happened?**
- Found duplicate 'b'
- Removed 'b' from left side
- Window shifted: ~~b~~ca~~b~~ → "cab"

---

## Iteration 6: right = 5, char = 'c' ⚠️ DUPLICATE!

```rust
// Check: is 'c' in set? YES! 🚨
// Enter while loop

While loop iteration 1:
  // Remove chars[2] = 'c'
  set.remove('c')
  set = {'a', 'b'}
  left = 3
  
  // Check: is 'c' in set? NO
  // Exit while loop

// Add 'c' to set
set = {'a', 'b', 'c'}

// Update max_area
window size = 5 - 3 + 1 = 3
max_area = max(3, 3) = 3

State:
  Window: "abc" [left=3, right=5]
  set = {'a', 'b', 'c'}
  max_area = 3
```

---

## Iteration 7: right = 6, char = 'b' ⚠️ DUPLICATE!

```rust
// Check: is 'b' in set? YES! 🚨
// Enter while loop

While loop iteration 1:
  // Remove chars[3] = 'a'
  set.remove('a')
  set = {'b', 'c'}
  left = 4
  
  // Check: is 'b' in set? YES! Still there!
  // Continue while loop

While loop iteration 2:
  // Remove chars[4] = 'b'
  set.remove('b')
  set = {'c'}
  left = 5
  
  // Check: is 'b' in set? NO
  // Exit while loop

// Add 'b' to set
set = {'c', 'b'}

// Update max_area
window size = 6 - 5 + 1 = 2
max_area = max(3, 2) = 3

State:
  Window: "cb" [left=5, right=6]
  set = {'c', 'b'}
  max_area = 3
```

**What happened?**
- Found duplicate 'b'
- Had to remove TWO characters ('a' and 'b') to eliminate the duplicate
- Window shrunk significantly

---

## Iteration 8: right = 7, char = 'b' ⚠️ DUPLICATE!

```rust
// Check: is 'b' in set? YES! 🚨
// Enter while loop

While loop iteration 1:
  // Remove chars[5] = 'c'
  set.remove('c')
  set = {'b'}
  left = 6
  
  // Check: is 'b' in set? YES!
  // Continue

While loop iteration 2:
  // Remove chars[6] = 'b'
  set.remove('b')
  set = {}
  left = 7
  
  // Check: is 'b' in set? NO
  // Exit while loop

// Add 'b' to set
set = {'b'}

// Update max_area
window size = 7 - 7 + 1 = 1
max_area = max(3, 1) = 3

State:
  Window: "b" [left=7, right=7]
  set = {'b'}
  max_area = 3
```

---

## Final Answer

```rust
max_area = 3

Output: 3
```

**Longest substring without repeating characters:** "abc" (length 3)

---

## 🎯 Pattern Summary

**The algorithm:**
1. **Expand** window with right pointer
2. **Shrink** from left when duplicate found
3. **Track** maximum window size

**Key insight:** 
- When you find a duplicate, keep removing from left **until the duplicate is gone**
- This might mean removing 1 character or multiple characters

**Visual of window movement:**
```
"abcabcbb"
 [abc]       → max = 3
  [bca]      → max = 3
   [cab]     → max = 3
    [abc]    → max = 3
     [bc]    → found duplicate 'b', shrink
      [cb]   → max = 3
       [b]   → found duplicate 'b', shrink heavily
```

Does this make it crystal clear? 💡
