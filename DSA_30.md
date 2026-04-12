# Top 30 DSA Interview Questions in JavaScript
### Basic to Advanced — Most Frequently Asked

---

## Table of Contents

### Arrays & Strings
1. [Two Sum](#q1-two-sum)
2. [Maximum Subarray — Kadane's Algorithm](#q2-maximum-subarray--kadanes-algorithm)
3. [Move Zeroes to End](#q3-move-zeroes-to-end)
4. [Rotate Array](#q4-rotate-array)
5. [Best Time to Buy and Sell Stock](#q5-best-time-to-buy-and-sell-stock)
6. [Valid Palindrome](#q6-valid-palindrome)
7. [Reverse a String](#q7-reverse-a-string)
8. [Anagram Check](#q8-anagram-check)
9. [Longest Substring Without Repeating Characters](#q9-longest-substring-without-repeating-characters)
10. [Find Duplicates in Array](#q10-find-duplicates-in-array)

### Linked Lists
11. [Reverse a Linked List](#q11-reverse-a-linked-list)
12. [Detect Cycle in Linked List](#q12-detect-cycle-in-linked-list)
13. [Merge Two Sorted Linked Lists](#q13-merge-two-sorted-linked-lists)
14. [Find Middle of Linked List](#q14-find-middle-of-linked-list)

### Stack & Queue
15. [Valid Parentheses](#q15-valid-parentheses)
16. [Implement Stack Using Queue](#q16-implement-stack-using-queue)
17. [Min Stack](#q17-min-stack)

### Sorting & Searching
18. [Binary Search](#q18-binary-search)
19. [Merge Sort](#q19-merge-sort)
20. [Quick Sort](#q20-quick-sort)

### Trees
21. [Inorder Traversal of Binary Tree](#q21-inorder-traversal-of-binary-tree)
22. [Maximum Depth of Binary Tree](#q22-maximum-depth-of-binary-tree)
23. [Check if Binary Tree is Balanced](#q23-check-if-binary-tree-is-balanced)
24. [Lowest Common Ancestor](#q24-lowest-common-ancestor)

### Graphs
25. [BFS — Breadth First Search](#q25-bfs--breadth-first-search)
26. [DFS — Depth First Search](#q26-dfs--depth-first-search)
27. [Number of Islands](#q27-number-of-islands)

### Dynamic Programming
28. [Fibonacci — Memoization vs Tabulation](#q28-fibonacci--memoization-vs-tabulation)
29. [Climbing Stairs](#q29-climbing-stairs)
30. [Longest Common Subsequence](#q30-longest-common-subsequence)

---

## Complexity Cheatsheet

| Data Structure | Access | Search | Insert | Delete |
|---------------|--------|--------|--------|--------|
| Array | O(1) | O(n) | O(n) | O(n) |
| Linked List | O(n) | O(n) | O(1) | O(1) |
| Stack | O(n) | O(n) | O(1) | O(1) |
| Queue | O(n) | O(n) | O(1) | O(1) |
| Hash Table | O(1) avg | O(1) avg | O(1) avg | O(1) avg |
| Binary Search Tree | O(log n) | O(log n) | O(log n) | O(log n) |

---

## Arrays & Strings

---

## Q1. Two Sum

**Problem:**
Given an array of numbers and a target, return the indices of the two numbers that add up to the target.

```
Input:  nums = [2, 7, 11, 15], target = 9
Output: [0, 1]   (because nums[0] + nums[1] = 2 + 7 = 9)
```

**Approach — Hash Map (Optimal):**
For each number, check if its complement (`target - num`) already exists in a map. If yes, we found our pair. If no, store current number with its index.

```javascript
function twoSum(nums, target) {
  const map = new Map(); // { number: index }

  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];

    if (map.has(complement)) {
      return [map.get(complement), i]; // Found the pair
    }

    map.set(nums[i], i); // Store current number
  }

  return []; // No solution found
}

// Test
console.log(twoSum([2, 7, 11, 15], 9));  // [0, 1]
console.log(twoSum([3, 2, 4], 6));        // [1, 2]
```

**Why Hash Map?**
- Brute force (nested loops) = O(n²) time
- Hash Map = O(n) time, O(n) space
- We trade space for speed

**Complexity:**
- Time: O(n)
- Space: O(n)

---

## Q2. Maximum Subarray — Kadane's Algorithm

**Problem:**
Find the contiguous subarray with the largest sum.

```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6   (subarray [4, -1, 2, 1])
```

**Approach — Kadane's Algorithm:**
At each position, decide: is it better to extend the current subarray OR start a new one from here? Keep track of the maximum seen so far.

```javascript
function maxSubArray(nums) {
  let currentSum = nums[0]; // Best sum ending at current position
  let maxSum = nums[0];     // Best sum seen overall

  for (let i = 1; i < nums.length; i++) {
    // Either extend current subarray OR start fresh from nums[i]
    currentSum = Math.max(nums[i], currentSum + nums[i]);

    // Update global maximum
    maxSum = Math.max(maxSum, currentSum);
  }

  return maxSum;
}

// Test
console.log(maxSubArray([-2, 1, -3, 4, -1, 2, 1, -5, 4])); // 6
console.log(maxSubArray([-1]));                               // -1
console.log(maxSubArray([5, 4, -1, 7, 8]));                  // 23
```

**Step-by-step trace for [-2, 1, -3, 4, -1, 2, 1, -5, 4]:**
```
i=0: current=-2,  max=-2
i=1: current=max(1, -2+1)=1,    max=1
i=2: current=max(-3, 1-3)=-2,   max=1
i=3: current=max(4, -2+4)=4,    max=4
i=4: current=max(-1, 4-1)=3,    max=4
i=5: current=max(2, 3+2)=5,     max=5
i=6: current=max(1, 5+1)=6,     max=6
i=7: current=max(-5, 6-5)=1,    max=6
i=8: current=max(4, 1+4)=5,     max=6
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Q3. Move Zeroes to End

**Problem:**
Move all zeroes to the end of the array while maintaining relative order of non-zero elements. Do it in-place.

```
Input:  [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

**Approach — Two Pointer:**
Use a slow pointer that tracks where the next non-zero element should go.

```javascript
function moveZeroes(nums) {
  let insertPos = 0; // Position to place next non-zero element

  // Move all non-zero elements to the front
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] !== 0) {
      nums[insertPos] = nums[i];
      insertPos++;
    }
  }

  // Fill remaining positions with zeroes
  for (let i = insertPos; i < nums.length; i++) {
    nums[i] = 0;
  }

  return nums;
}

// Test
console.log(moveZeroes([0, 1, 0, 3, 12])); // [1, 3, 12, 0, 0]
console.log(moveZeroes([0, 0, 1]));          // [1, 0, 0]
```

**Complexity:**
- Time: O(n)
- Space: O(1) — in-place

---

## Q4. Rotate Array

**Problem:**
Rotate an array to the right by k steps.

```
Input:  [1, 2, 3, 4, 5, 6, 7], k = 3
Output: [5, 6, 7, 1, 2, 3, 4]
```

**Approach — Three Reverse Trick:**
1. Reverse the entire array
2. Reverse first k elements
3. Reverse remaining elements

```javascript
function rotate(nums, k) {
  k = k % nums.length; // Handle k > array length

  function reverse(arr, start, end) {
    while (start < end) {
      [arr[start], arr[end]] = [arr[end], arr[start]]; // Swap
      start++;
      end--;
    }
  }

  reverse(nums, 0, nums.length - 1); // Reverse all:     [7,6,5,4,3,2,1]
  reverse(nums, 0, k - 1);           // Reverse first k: [5,6,7,4,3,2,1]
  reverse(nums, k, nums.length - 1); // Reverse rest:    [5,6,7,1,2,3,4]

  return nums;
}

// Test
console.log(rotate([1, 2, 3, 4, 5, 6, 7], 3)); // [5, 6, 7, 1, 2, 3, 4]
console.log(rotate([-1, -100, 3, 99], 2));       // [3, 99, -1, -100]
```

**Complexity:**
- Time: O(n)
- Space: O(1) — in-place

---

## Q5. Best Time to Buy and Sell Stock

**Problem:**
Given an array where `prices[i]` is the price on day i, find the maximum profit from one buy + one sell.

```
Input:  [7, 1, 5, 3, 6, 4]
Output: 5   (buy at 1, sell at 6)
```

**Approach — One Pass:**
Track the minimum price seen so far (best buy day) and the maximum profit achievable.

```javascript
function maxProfit(prices) {
  let minPrice = Infinity; // Lowest buy price seen
  let maxProfit = 0;       // Best profit seen

  for (let price of prices) {
    if (price < minPrice) {
      minPrice = price;    // Found a better day to buy
    } else if (price - minPrice > maxProfit) {
      maxProfit = price - minPrice; // Found a better profit
    }
  }

  return maxProfit;
}

// Test
console.log(maxProfit([7, 1, 5, 3, 6, 4])); // 5
console.log(maxProfit([7, 6, 4, 3, 1]));     // 0 (no profit possible)
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Q6. Valid Palindrome

**Problem:**
Given a string, check if it is a palindrome considering only alphanumeric characters and ignoring case.

```
Input:  "A man, a plan, a canal: Panama"
Output: true

Input:  "race a car"
Output: false
```

**Approach — Two Pointer:**
Use two pointers from both ends, skip non-alphanumeric characters, compare characters.

```javascript
function isPalindrome(s) {
  let left = 0;
  let right = s.length - 1;

  const isAlphanumeric = (char) => /[a-zA-Z0-9]/.test(char);

  while (left < right) {
    // Skip non-alphanumeric from left
    while (left < right && !isAlphanumeric(s[left])) left++;
    // Skip non-alphanumeric from right
    while (left < right && !isAlphanumeric(s[right])) right--;

    // Compare characters (case-insensitive)
    if (s[left].toLowerCase() !== s[right].toLowerCase()) {
      return false;
    }

    left++;
    right--;
  }

  return true;
}

// Test
console.log(isPalindrome("A man, a plan, a canal: Panama")); // true
console.log(isPalindrome("race a car"));                      // false
console.log(isPalindrome(" "));                               // true
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Q7. Reverse a String

**Problem:**
Reverse a string in-place (given as a character array).

```
Input:  ['h','e','l','l','o']
Output: ['o','l','l','e','h']
```

**Approach — Two Pointer Swap:**

```javascript
function reverseString(s) {
  let left = 0;
  let right = s.length - 1;

  while (left < right) {
    [s[left], s[right]] = [s[right], s[left]]; // Swap
    left++;
    right--;
  }

  return s;
}

// Reverse a string (not array)
function reverseStr(str) {
  return str.split('').reverse().join('');
  // OR manually:
  // return str.split('').reduce((acc, char) => char + acc, '');
}

// Test
console.log(reverseString(['h','e','l','l','o'])); // ['o','l','l','e','h']
console.log(reverseStr("hello"));                   // "olleh"
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Q8. Anagram Check

**Problem:**
Given two strings, check if one is an anagram of the other (same characters, same frequency, different order).

```
Input:  s = "anagram", t = "nagaram"
Output: true

Input:  s = "rat", t = "car"
Output: false
```

**Approach — Character Frequency Map:**

```javascript
function isAnagram(s, t) {
  if (s.length !== t.length) return false;

  const freq = {};

  // Count characters in s
  for (let char of s) {
    freq[char] = (freq[char] || 0) + 1;
  }

  // Decrement count for characters in t
  for (let char of t) {
    if (!freq[char]) return false; // Missing or extra character
    freq[char]--;
  }

  return true;
}

// Alternative: Sort both strings and compare
function isAnagramSort(s, t) {
  return s.split('').sort().join('') === t.split('').sort().join('');
  // Time: O(n log n) — worse than frequency map
}

// Test
console.log(isAnagram("anagram", "nagaram")); // true
console.log(isAnagram("rat", "car"));          // false
```

**Complexity:**
- Time: O(n)
- Space: O(1) — at most 26 lowercase letters

---

## Q9. Longest Substring Without Repeating Characters

**Problem:**
Find the length of the longest substring without any repeating characters.

```
Input:  "abcabcbb"
Output: 3   (substring "abc")

Input:  "pwwkew"
Output: 3   (substring "wke")
```

**Approach — Sliding Window:**
Use two pointers (window boundaries) and a Set to track unique characters. Expand right, shrink left when duplicate found.

```javascript
function lengthOfLongestSubstring(s) {
  const charSet = new Set();
  let left = 0;
  let maxLength = 0;

  for (let right = 0; right < s.length; right++) {
    // If duplicate found, shrink window from left until duplicate is removed
    while (charSet.has(s[right])) {
      charSet.delete(s[left]);
      left++;
    }

    charSet.add(s[right]);
    maxLength = Math.max(maxLength, right - left + 1);
  }

  return maxLength;
}

// Test
console.log(lengthOfLongestSubstring("abcabcbb")); // 3
console.log(lengthOfLongestSubstring("bbbbb"));    // 1
console.log(lengthOfLongestSubstring("pwwkew"));   // 3
```

**Step trace for "abcabcbb":**
```
right=0 (a): set={a},    left=0, max=1
right=1 (b): set={a,b},  left=0, max=2
right=2 (c): set={a,b,c},left=0, max=3
right=3 (a): 'a' exists! remove 'a', left=1. set={b,c,a}, max=3
right=4 (b): 'b' exists! remove 'b', left=2. set={c,a,b}, max=3
right=5 (c): 'c' exists! remove 'c', left=3. set={a,b,c}, max=3
right=6 (b): 'b' exists! remove 'a', 'b', left=5. set={c,b}, max=3
right=7 (b): 'b' exists! remove 'b', left=6. set={b}, max=3
```

**Complexity:**
- Time: O(n)
- Space: O(min(n, m)) where m = charset size

---

## Q10. Find Duplicates in Array

**Problem:**
Find all duplicate elements in an array of integers where 1 ≤ a[i] ≤ n.

```
Input:  [4, 3, 2, 7, 8, 2, 3, 1]
Output: [2, 3]
```

**Approach — Index Marking (O(1) space):**
Use the sign of the element at index `abs(num)-1` as a visited marker.

```javascript
function findDuplicates(nums) {
  const result = [];

  for (let i = 0; i < nums.length; i++) {
    const index = Math.abs(nums[i]) - 1;

    if (nums[index] < 0) {
      result.push(Math.abs(nums[i])); // Already visited → duplicate
    } else {
      nums[index] = -nums[index]; // Mark as visited by negating
    }
  }

  return result;
}

// Simple approach using Set (easier to understand)
function findDuplicatesSet(nums) {
  const seen = new Set();
  const duplicates = [];

  for (let num of nums) {
    if (seen.has(num)) {
      duplicates.push(num);
    } else {
      seen.add(num);
    }
  }

  return duplicates;
}

// Test
console.log(findDuplicates([4, 3, 2, 7, 8, 2, 3, 1])); // [2, 3]
```

**Complexity:**
- Index Marking: Time O(n), Space O(1)
- Set approach: Time O(n), Space O(n)

---

## Linked Lists

---

## Q11. Reverse a Linked List

**Problem:**
Reverse a singly linked list.

```
Input:  1 → 2 → 3 → 4 → 5
Output: 5 → 4 → 3 → 2 → 1
```

**Node Definition:**
```javascript
class ListNode {
  constructor(val, next = null) {
    this.val = val;
    this.next = next;
  }
}
```

**Approach — Iterative (Three Pointer):**

```javascript
function reverseList(head) {
  let prev = null;
  let current = head;

  while (current !== null) {
    const next = current.next; // Save next node
    current.next = prev;       // Reverse the pointer
    prev = current;            // Move prev forward
    current = next;            // Move current forward
  }

  return prev; // prev is now the new head
}

// Recursive approach
function reverseListRecursive(head) {
  if (!head || !head.next) return head;

  const newHead = reverseListRecursive(head.next); // Recurse to end
  head.next.next = head; // Reverse the pointer
  head.next = null;      // Remove old forward pointer
  return newHead;
}
```

**Step trace for 1 → 2 → 3:**
```
Initial: prev=null, curr=1
Step 1:  next=2, curr.next=null, prev=1, curr=2   → null ← 1  2 → 3
Step 2:  next=3, curr.next=1,   prev=2, curr=3   → null ← 1 ← 2  3
Step 3:  next=null, curr.next=2, prev=3, curr=null → null ← 1 ← 2 ← 3
Return prev=3 (new head)
```

**Complexity:**
- Time: O(n)
- Space: O(1) iterative, O(n) recursive (call stack)

---

## Q12. Detect Cycle in Linked List

**Problem:**
Determine if a linked list has a cycle.

**Approach — Floyd's Cycle Detection (Tortoise and Hare):**
Use two pointers — slow moves 1 step, fast moves 2 steps. If there's a cycle, they will meet.

```javascript
function hasCycle(head) {
  let slow = head;
  let fast = head;

  while (fast !== null && fast.next !== null) {
    slow = slow.next;       // Move 1 step
    fast = fast.next.next;  // Move 2 steps

    if (slow === fast) return true; // They met → cycle exists
  }

  return false; // Fast reached end → no cycle
}

// Find the START of the cycle
function detectCycleStart(head) {
  let slow = head;
  let fast = head;

  // Phase 1: Detect cycle
  while (fast !== null && fast.next !== null) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) break;
  }

  if (!fast || !fast.next) return null; // No cycle

  // Phase 2: Find cycle start
  slow = head;
  while (slow !== fast) {
    slow = slow.next;
    fast = fast.next; // Both move 1 step now
  }

  return slow; // Cycle start node
}
```

**Why does it work?**
In a list of length L with cycle of length C, slow and fast will meet after `C - (L mod C)` steps inside the cycle. The math guarantees they always meet if a cycle exists.

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Q13. Merge Two Sorted Linked Lists

**Problem:**
Merge two sorted linked lists into one sorted linked list.

```
Input:  1 → 2 → 4  and  1 → 3 → 4
Output: 1 → 1 → 2 → 3 → 4 → 4
```

**Approach — Iterative with Dummy Node:**

```javascript
function mergeTwoLists(l1, l2) {
  const dummy = new ListNode(0); // Dummy head to simplify edge cases
  let current = dummy;

  while (l1 !== null && l2 !== null) {
    if (l1.val <= l2.val) {
      current.next = l1;
      l1 = l1.next;
    } else {
      current.next = l2;
      l2 = l2.next;
    }
    current = current.next;
  }

  // Attach remaining list (one of them is null)
  current.next = l1 !== null ? l1 : l2;

  return dummy.next; // Return real head (skip dummy)
}

// Recursive approach
function mergeTwoListsRecursive(l1, l2) {
  if (!l1) return l2;
  if (!l2) return l1;

  if (l1.val <= l2.val) {
    l1.next = mergeTwoListsRecursive(l1.next, l2);
    return l1;
  } else {
    l2.next = mergeTwoListsRecursive(l1, l2.next);
    return l2;
  }
}
```

**Complexity:**
- Time: O(m + n) where m, n are lengths of the two lists
- Space: O(1) iterative, O(m + n) recursive

---

## Q14. Find Middle of Linked List

**Problem:**
Find the middle node of a linked list. If two middles exist, return the second one.

```
Input:  1 → 2 → 3 → 4 → 5
Output: Node with value 3

Input:  1 → 2 → 3 → 4
Output: Node with value 3 (second middle)
```

**Approach — Slow and Fast Pointer:**

```javascript
function middleNode(head) {
  let slow = head;
  let fast = head;

  // When fast reaches end, slow is at middle
  while (fast !== null && fast.next !== null) {
    slow = slow.next;       // 1 step
    fast = fast.next.next;  // 2 steps
  }

  return slow; // Middle node
}
```

**Trace for 1 → 2 → 3 → 4 → 5:**
```
Start: slow=1, fast=1
Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Step 3: fast.next=null → stop
Return slow=3 ✓
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Stack & Queue

---

## Q15. Valid Parentheses

**Problem:**
Given a string of brackets, determine if the brackets are correctly matched and nested.

```
Input:  "()[]{}"  → true
Input:  "([)]"    → false
Input:  "{[]}"    → true
```

**Approach — Stack:**
Push opening brackets onto stack. When closing bracket found, check if top of stack is the matching opener.

```javascript
function isValid(s) {
  const stack = [];
  const map = {
    ')': '(',
    ']': '[',
    '}': '{',
  };

  for (let char of s) {
    if (char === '(' || char === '[' || char === '{') {
      stack.push(char); // Opening bracket → push
    } else {
      // Closing bracket → check if matches top of stack
      if (stack.length === 0 || stack[stack.length - 1] !== map[char]) {
        return false;
      }
      stack.pop();
    }
  }

  return stack.length === 0; // Stack must be empty for valid string
}

// Test
console.log(isValid("()[]{}"));  // true
console.log(isValid("([)]"));    // false
console.log(isValid("{[]}"));    // true
console.log(isValid("]"));       // false
```

**Complexity:**
- Time: O(n)
- Space: O(n)

---

## Q16. Implement Stack Using Queue

**Problem:**
Implement a Stack (LIFO) using only Queue operations (enqueue, dequeue).

**Approach — Two Queues:**

```javascript
class MyStack {
  constructor() {
    this.queue1 = []; // Main queue
    this.queue2 = []; // Helper queue
  }

  // Push: Add element and rotate so new element is at front
  push(x) {
    this.queue2.push(x);

    // Move all elements from queue1 to queue2
    while (this.queue1.length > 0) {
      this.queue2.push(this.queue1.shift());
    }

    // Swap queues
    [this.queue1, this.queue2] = [this.queue2, this.queue1];
  }

  // Pop: Remove front of queue1 (which is the last pushed element)
  pop() {
    return this.queue1.shift();
  }

  // Top: Peek front of queue1
  top() {
    return this.queue1[0];
  }

  empty() {
    return this.queue1.length === 0;
  }
}

// Test
const stack = new MyStack();
stack.push(1);
stack.push(2);
console.log(stack.top());  // 2
console.log(stack.pop());  // 2
console.log(stack.empty()); // false
```

**Complexity:**
- Push: O(n)
- Pop: O(1)
- Top: O(1)

---

## Q17. Min Stack

**Problem:**
Design a stack that supports push, pop, top, and retrieving the minimum element in O(1).

**Approach — Auxiliary Min Stack:**
Maintain a second stack that tracks the current minimum at every level.

```javascript
class MinStack {
  constructor() {
    this.stack = [];
    this.minStack = []; // Tracks minimum at each level
  }

  push(val) {
    this.stack.push(val);

    // Push to minStack: new min is either val or current min
    const currentMin = this.minStack.length === 0
      ? val
      : Math.min(val, this.minStack[this.minStack.length - 1]);

    this.minStack.push(currentMin);
  }

  pop() {
    this.stack.pop();
    this.minStack.pop(); // Both stacks pop together
  }

  top() {
    return this.stack[this.stack.length - 1];
  }

  getMin() {
    return this.minStack[this.minStack.length - 1]; // Always O(1)
  }
}

// Test
const minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
console.log(minStack.getMin()); // -3
minStack.pop();
console.log(minStack.top());    // 0
console.log(minStack.getMin()); // -2
```

**Complexity:**
- All operations: O(1)
- Space: O(n)

---

## Sorting & Searching

---

## Q18. Binary Search

**Problem:**
Search for a target in a sorted array. Return its index, or -1 if not found.

```
Input:  nums = [-1, 0, 3, 5, 9, 12], target = 9
Output: 4
```

**Approach — Divide and Conquer:**
Check the middle element. If it equals target, return. If target is smaller, search left half. If larger, search right half.

```javascript
function binarySearch(nums, target) {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (nums[mid] === target) {
      return mid;              // Found
    } else if (nums[mid] < target) {
      left = mid + 1;          // Search right half
    } else {
      right = mid - 1;         // Search left half
    }
  }

  return -1; // Not found
}

// Recursive version
function binarySearchRecursive(nums, target, left = 0, right = nums.length - 1) {
  if (left > right) return -1;

  const mid = Math.floor((left + right) / 2);

  if (nums[mid] === target) return mid;
  if (nums[mid] < target) return binarySearchRecursive(nums, target, mid + 1, right);
  return binarySearchRecursive(nums, target, left, mid - 1);
}

// Test
console.log(binarySearch([-1, 0, 3, 5, 9, 12], 9));  // 4
console.log(binarySearch([-1, 0, 3, 5, 9, 12], 2));  // -1
```

**Complexity:**
- Time: O(log n) — halves the search space each step
- Space: O(1) iterative, O(log n) recursive

---

## Q19. Merge Sort

**Problem:**
Sort an array using the Merge Sort algorithm.

**Approach — Divide and Conquer:**
1. Divide array into two halves
2. Recursively sort each half
3. Merge the two sorted halves

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr; // Base case

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));  // Sort left half
  const right = mergeSort(arr.slice(mid));    // Sort right half

  return merge(left, right);
}

function merge(left, right) {
  const result = [];
  let i = 0, j = 0;

  // Compare elements from both halves and pick smaller
  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      result.push(left[i++]);
    } else {
      result.push(right[j++]);
    }
  }

  // Append remaining elements
  return result.concat(left.slice(i)).concat(right.slice(j));
}

// Test
console.log(mergeSort([38, 27, 43, 3, 9, 82, 10]));
// [3, 9, 10, 27, 38, 43, 82]
```

**Complexity:**
- Time: O(n log n) — always (best, average, worst)
- Space: O(n) — needs extra array for merging

**Why Merge Sort?**
- Stable sort (preserves order of equal elements)
- Predictable O(n log n) — no worst case like QuickSort
- Great for sorting linked lists

---

## Q20. Quick Sort

**Problem:**
Sort an array using Quick Sort algorithm.

**Approach — Partition around Pivot:**
1. Choose a pivot element
2. Partition: put smaller elements left, larger elements right
3. Recursively sort both partitions

```javascript
function quickSort(arr, low = 0, high = arr.length - 1) {
  if (low < high) {
    const pivotIndex = partition(arr, low, high);
    quickSort(arr, low, pivotIndex - 1);  // Sort left of pivot
    quickSort(arr, pivotIndex + 1, high); // Sort right of pivot
  }
  return arr;
}

function partition(arr, low, high) {
  const pivot = arr[high]; // Choose last element as pivot
  let i = low - 1;          // Index of smaller element

  for (let j = low; j < high; j++) {
    if (arr[j] <= pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]]; // Swap
    }
  }

  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]]; // Place pivot correctly
  return i + 1;
}

// Test
console.log(quickSort([10, 7, 8, 9, 1, 5]));
// [1, 5, 7, 8, 9, 10]
```

**Complexity:**
- Time: O(n log n) average, O(n²) worst case (sorted array with bad pivot)
- Space: O(log n) avg recursion depth

**Merge Sort vs Quick Sort:**

| Feature | Merge Sort | Quick Sort |
|---------|-----------|------------|
| Time (avg) | O(n log n) | O(n log n) |
| Time (worst) | O(n log n) | O(n²) |
| Space | O(n) | O(log n) |
| Stable | Yes | No |
| In-place | No | Yes |
| Preferred for | Linked lists, stable sort | Arrays (cache-friendly) |

---

## Trees

---

## Q21. Inorder Traversal of Binary Tree

**Problem:**
Return the inorder traversal of a binary tree (Left → Root → Right).

```
      1
     / \
    2   3
   / \
  4   5

Inorder: [4, 2, 5, 1, 3]
```

**Node Definition:**
```javascript
class TreeNode {
  constructor(val, left = null, right = null) {
    this.val = val;
    this.left = left;
    this.right = right;
  }
}
```

```javascript
// Recursive
function inorderTraversal(root) {
  const result = [];

  function traverse(node) {
    if (!node) return;
    traverse(node.left);   // Left
    result.push(node.val); // Root
    traverse(node.right);  // Right
  }

  traverse(root);
  return result;
}

// Iterative using Stack
function inorderIterative(root) {
  const result = [];
  const stack = [];
  let current = root;

  while (current || stack.length > 0) {
    // Go as left as possible
    while (current) {
      stack.push(current);
      current = current.left;
    }

    current = stack.pop();        // Process node
    result.push(current.val);
    current = current.right;      // Move to right subtree
  }

  return result;
}
```

**Tree Traversal Types:**
- **Inorder** (L → Root → R): Gives sorted output for BST
- **Preorder** (Root → L → R): Used to copy/serialize a tree
- **Postorder** (L → R → Root): Used to delete a tree
- **Level Order** (BFS): Level by level

**Complexity:**
- Time: O(n)
- Space: O(n) — for stack/result

---

## Q22. Maximum Depth of Binary Tree

**Problem:**
Find the maximum depth (height) of a binary tree.

```
      3
     / \
    9  20
       / \
      15   7

Output: 3
```

```javascript
// Recursive DFS
function maxDepth(root) {
  if (!root) return 0;

  const leftDepth = maxDepth(root.left);
  const rightDepth = maxDepth(root.right);

  return 1 + Math.max(leftDepth, rightDepth);
}

// Iterative BFS (level count)
function maxDepthBFS(root) {
  if (!root) return 0;

  const queue = [root];
  let depth = 0;

  while (queue.length > 0) {
    depth++;
    const levelSize = queue.length;

    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
  }

  return depth;
}

// Test
// Tree: 3 → (9, 20 → (15, 7))
console.log(maxDepth(root)); // 3
```

**Complexity:**
- Time: O(n)
- Space: O(h) where h = height (O(n) worst for skewed tree)

---

## Q23. Check if Binary Tree is Balanced

**Problem:**
A balanced tree is one where the height difference between left and right subtrees of every node is at most 1.

```
      1
     / \
    2   3
   /
  4

Left height = 3, Right height = 1 → Difference = 2 → NOT balanced
```

```javascript
function isBalanced(root) {
  // Returns height if balanced, -1 if not
  function checkHeight(node) {
    if (!node) return 0;

    const leftHeight = checkHeight(node.left);
    if (leftHeight === -1) return -1; // Left subtree is unbalanced

    const rightHeight = checkHeight(node.right);
    if (rightHeight === -1) return -1; // Right subtree is unbalanced

    // Check current node's balance
    if (Math.abs(leftHeight - rightHeight) > 1) return -1;

    return 1 + Math.max(leftHeight, rightHeight); // Return height
  }

  return checkHeight(root) !== -1;
}

// Test
console.log(isBalanced(root)); // true or false
```

**Why return -1 instead of false?**
We need to propagate two pieces of information up: whether it's balanced AND the height. Using -1 as a sentinel value for "unbalanced" allows us to combine both in one integer return.

**Complexity:**
- Time: O(n) — visits each node once
- Space: O(h)

---

## Q24. Lowest Common Ancestor

**Problem:**
Find the lowest common ancestor (LCA) of two nodes in a binary tree.

```
         3
        / \
       5   1
      / \ / \
     6  2 0  8
       / \
      7   4

LCA(5, 1) = 3
LCA(5, 4) = 5
```

```javascript
function lowestCommonAncestor(root, p, q) {
  if (!root) return null;

  // If current node is one of the targets, it's the LCA
  if (root === p || root === q) return root;

  const left = lowestCommonAncestor(root.left, p, q);
  const right = lowestCommonAncestor(root.right, p, q);

  // If both sides found something → current node is LCA
  if (left && right) return root;

  // Otherwise return whichever side found something
  return left || right;
}
```

**Logic Explained:**
- If we find p or q, return that node up
- If both left AND right returned non-null, that means p and q are in different subtrees → current node is their LCA
- If only one side returned non-null, propagate that up

**Complexity:**
- Time: O(n)
- Space: O(h)

---

## Graphs

---

## Q25. BFS — Breadth First Search

**Problem:**
Traverse a graph level by level using BFS.

**Approach — Queue:**
Start from a source node, visit all neighbors at current level before going deeper.

```javascript
function bfs(graph, start) {
  const visited = new Set();
  const queue = [start];
  const result = [];

  visited.add(start);

  while (queue.length > 0) {
    const node = queue.shift(); // Dequeue front
    result.push(node);

    // Add unvisited neighbors
    for (let neighbor of graph[node]) {
      if (!visited.has(neighbor)) {
        visited.add(neighbor);
        queue.push(neighbor);
      }
    }
  }

  return result;
}

// Test
const graph = {
  0: [1, 2],
  1: [0, 3, 4],
  2: [0, 5],
  3: [1],
  4: [1],
  5: [2],
};

console.log(bfs(graph, 0)); // [0, 1, 2, 3, 4, 5]
```

**BFS vs DFS:**

| Feature | BFS | DFS |
|---------|-----|-----|
| Data Structure | Queue | Stack / Recursion |
| Traversal | Level by level | Deep path first |
| Shortest Path | Yes (unweighted) | No |
| Memory | O(w) — width of graph | O(h) — height of graph |
| Use cases | Shortest path, level order | Cycle detection, topological sort |

**Complexity:**
- Time: O(V + E) — vertices + edges
- Space: O(V)

---

## Q26. DFS — Depth First Search

**Problem:**
Traverse a graph by going as deep as possible before backtracking.

```javascript
// Recursive DFS
function dfsRecursive(graph, node, visited = new Set(), result = []) {
  visited.add(node);
  result.push(node);

  for (let neighbor of graph[node]) {
    if (!visited.has(neighbor)) {
      dfsRecursive(graph, neighbor, visited, result);
    }
  }

  return result;
}

// Iterative DFS using Stack
function dfsIterative(graph, start) {
  const visited = new Set();
  const stack = [start];
  const result = [];

  while (stack.length > 0) {
    const node = stack.pop(); // Pop from top (LIFO)

    if (!visited.has(node)) {
      visited.add(node);
      result.push(node);

      for (let neighbor of graph[node]) {
        if (!visited.has(neighbor)) {
          stack.push(neighbor);
        }
      }
    }
  }

  return result;
}

// Test
console.log(dfsRecursive(graph, 0));  // [0, 1, 3, 4, 2, 5]
console.log(dfsIterative(graph, 0));  // [0, 2, 5, 1, 4, 3]
```

**Complexity:**
- Time: O(V + E)
- Space: O(V)

---

## Q27. Number of Islands

**Problem:**
Given a 2D grid of '1's (land) and '0's (water), count the number of islands.

```
Input:
11110
11010
11000
00000

Output: 1

Input:
11000
11000
00100
00011

Output: 3
```

**Approach — DFS Flood Fill:**
When we find a '1', it's a new island. Do DFS to mark all connected land as visited ('0').

```javascript
function numIslands(grid) {
  if (!grid || grid.length === 0) return 0;

  let count = 0;
  const rows = grid.length;
  const cols = grid[0].length;

  function dfs(r, c) {
    // Out of bounds or water → stop
    if (r < 0 || r >= rows || c < 0 || c >= cols || grid[r][c] === '0') return;

    grid[r][c] = '0'; // Mark as visited (sink the island)

    // Explore all 4 directions
    dfs(r + 1, c); // Down
    dfs(r - 1, c); // Up
    dfs(r, c + 1); // Right
    dfs(r, c - 1); // Left
  }

  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (grid[r][c] === '1') {
        count++;       // Found new island
        dfs(r, c);     // Sink entire island
      }
    }
  }

  return count;
}

// Test
const grid = [
  ['1','1','0','0','0'],
  ['1','1','0','0','0'],
  ['0','0','1','0','0'],
  ['0','0','0','1','1'],
];
console.log(numIslands(grid)); // 3
```

**Complexity:**
- Time: O(m × n) — each cell visited at most once
- Space: O(m × n) — DFS call stack in worst case

---

## Dynamic Programming

---

## Q28. Fibonacci — Memoization vs Tabulation

**Problem:**
Return the nth Fibonacci number. F(0)=0, F(1)=1, F(n)=F(n-1)+F(n-2)

**Approach 1 — Naive Recursion (Bad):**
```javascript
function fibNaive(n) {
  if (n <= 1) return n;
  return fibNaive(n - 1) + fibNaive(n - 2);
  // Time: O(2^n) — exponential! Many repeated calculations
}
```

**Approach 2 — Memoization (Top-Down DP):**
Cache results of subproblems to avoid recalculation.

```javascript
function fibMemo(n, memo = {}) {
  if (n in memo) return memo[n];  // Return cached result
  if (n <= 1) return n;

  memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
  return memo[n];
}

console.log(fibMemo(50)); // 12586269025 — instant!
// Time: O(n), Space: O(n)
```

**Approach 3 — Tabulation (Bottom-Up DP):**
Build the solution iteratively from smaller subproblems.

```javascript
function fibTab(n) {
  if (n <= 1) return n;

  const dp = new Array(n + 1);
  dp[0] = 0;
  dp[1] = 1;

  for (let i = 2; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }

  return dp[n];
}

// Space-optimized tabulation
function fibOptimal(n) {
  if (n <= 1) return n;

  let prev2 = 0, prev1 = 1;

  for (let i = 2; i <= n; i++) {
    const current = prev1 + prev2;
    prev2 = prev1;
    prev1 = current;
  }

  return prev1;
}

// Time: O(n), Space: O(1)
console.log(fibOptimal(10)); // 55
```

**DP Comparison:**

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Naive recursion | O(2^n) | O(n) | Too slow |
| Memoization | O(n) | O(n) | Top-down, easy to write |
| Tabulation | O(n) | O(n) | Bottom-up, iterative |
| Space-optimized | O(n) | O(1) | Best for Fibonacci |

---

## Q29. Climbing Stairs

**Problem:**
You can climb 1 or 2 steps at a time. How many distinct ways can you climb to the nth step?

```
n = 3 → 3 ways: [1,1,1], [1,2], [2,1]
n = 4 → 5 ways
```

**Key Insight:**
This is exactly the Fibonacci sequence! To reach step n, you come from step n-1 (1 step) or step n-2 (2 steps). So `ways(n) = ways(n-1) + ways(n-2)`.

```javascript
function climbStairs(n) {
  if (n <= 2) return n;

  let prev2 = 1; // ways to reach step 1
  let prev1 = 2; // ways to reach step 2

  for (let i = 3; i <= n; i++) {
    const current = prev1 + prev2;
    prev2 = prev1;
    prev1 = current;
  }

  return prev1;
}

// Using DP table (easier to understand)
function climbStairsDP(n) {
  const dp = new Array(n + 1);
  dp[1] = 1;
  dp[2] = 2;

  for (let i = 3; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }

  return dp[n];
}

// Test
console.log(climbStairs(2)); // 2
console.log(climbStairs(3)); // 3
console.log(climbStairs(5)); // 8
```

**Sequence:** 1, 2, 3, 5, 8, 13... (Fibonacci starting from 1, 2)

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Q30. Longest Common Subsequence

**Problem:**
Find the length of the longest common subsequence (LCS) of two strings. A subsequence is a sequence that appears in both strings in the same order but not necessarily consecutively.

```
Input:  text1 = "abcde", text2 = "ace"
Output: 3   (LCS is "ace")

Input:  text1 = "abc", text2 = "abc"
Output: 3

Input:  text1 = "abc", text2 = "def"
Output: 0
```

**Approach — 2D DP Table:**
Build a table where `dp[i][j]` = length of LCS of `text1[0..i-1]` and `text2[0..j-1]`.

**Recurrence:**
- If `text1[i-1] === text2[j-1]`: `dp[i][j] = dp[i-1][j-1] + 1` (characters match — extend LCS)
- Else: `dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1])` (take best without one character)

```javascript
function longestCommonSubsequence(text1, text2) {
  const m = text1.length;
  const n = text2.length;

  // Create (m+1) x (n+1) table initialized to 0
  const dp = Array.from({ length: m + 1 }, () => new Array(n + 1).fill(0));

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (text1[i - 1] === text2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1; // Characters match
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]); // Take best
      }
    }
  }

  return dp[m][n];
}

// Test
console.log(longestCommonSubsequence("abcde", "ace")); // 3
console.log(longestCommonSubsequence("abc", "abc"));    // 3
console.log(longestCommonSubsequence("abc", "def"));    // 0
```

**DP Table for "abcde" and "ace":**
```
    ""  a  c  e
""   0  0  0  0
a    0  1  1  1
b    0  1  1  1
c    0  1  2  2
d    0  1  2  2
e    0  1  2  3   ← Answer: 3
```

**Complexity:**
- Time: O(m × n)
- Space: O(m × n) — can be optimized to O(n) using two rows

---

## Big-O Complexity Summary

### Common Time Complexities (Best to Worst)
```
O(1)        → Constant   — Hash map lookup, array access
O(log n)    → Logarithmic — Binary search, balanced BST
O(n)        → Linear     — Single loop, linear scan
O(n log n)  → Linearithmic — Merge sort, heap sort
O(n²)       → Quadratic  — Nested loops, bubble sort
O(2^n)      → Exponential — Naive recursion, subsets
O(n!)       → Factorial   — Permutations
```

### Key Patterns to Remember

| Pattern | Best For | Key Idea |
|---------|----------|----------|
| Two Pointer | Sorted arrays, strings | Left + right pointers moving inward or same direction |
| Sliding Window | Subarray/substring problems | Expand right, shrink left |
| Hash Map | Frequency, lookup, two sum | O(1) lookup trades space for speed |
| Fast & Slow Pointer | Linked list cycle, middle | Fast moves 2x speed of slow |
| Binary Search | Sorted array search | Eliminate half each step |
| BFS | Shortest path, level order | Queue, visit neighbors first |
| DFS | Paths, cycle detection | Stack/recursion, go deep first |
| Dynamic Programming | Overlapping subproblems | Cache results, build bottom-up |
| Divide & Conquer | Sorting, tree problems | Split, solve, merge |

---

## Interview Tips

- **Always clarify:** Ask about input size, edge cases (empty array, negative numbers, duplicates), and expected output format before coding
- **Think aloud:** Interviewers want to see your thought process — narrate while you solve
- **Start brute force:** Mention the naive solution first, then optimize. Shows you understand the problem
- **State complexity:** After writing solution, always state Time and Space complexity
- **Test your code:** Walk through 2–3 test cases including edge cases before saying you're done
- **Know your patterns:** Most DSA problems are variants of the patterns in the table above

---

*Practice each of these problems multiple times. Speed comes with repetition. Focus on understanding the pattern, not memorizing the code.*
