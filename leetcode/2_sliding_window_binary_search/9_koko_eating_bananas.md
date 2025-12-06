Input: piles = [3, 6, 7, 11], h = 8

Step 1: Initialize search range
  left = 1
  right = 11 (max pile)

Iteration 1:
  mid = 6
  Hours at speed 6:
    pile 3:  ⌈3/6⌉ = 1
    pile 6:  ⌈6/6⌉ = 1
    pile 7:  ⌈7/6⌉ = 2
    pile 11: ⌈11/6⌉ = 2
    Total: 6 hours
  
  6 <= 8? YES → Can finish in time
  Try slower speed: right = 6

Iteration 2:
  left = 1, right = 6
  mid = 3
  Hours at speed 3:
    pile 3:  1 hour
    pile 6:  2 hours
    pile 7:  3 hours
    pile 11: 4 hours
    Total: 10 hours
  
  10 <= 8? NO → Too slow!
  Need faster: left = 4

Iteration 3:
  left = 4, right = 6
  mid = 5
  Hours at speed 5:
    pile 3:  1
    pile 6:  2
    pile 7:  2
    pile 11: 3
    Total: 8 hours
  
  8 <= 8? YES
  Try slower: right = 5

Iteration 4:
  left = 4, right = 5
  mid = 4
  Hours at speed 4:
    pile 3:  1
    pile 6:  2
    pile 7:  2
    pile 11: 3
    Total: 8 hours
  
  8 <= 8? YES
  Try slower: right = 4

Iteration 5:
  left = 4, right = 4
  Loop exits (left == right)

Output: 4
