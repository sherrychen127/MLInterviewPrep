Absolutely. For interview prep, I’d organize algorithms around **“what shape does the problem have?” → “what tool should that trigger?”** rather than memorizing isolated solutions.

Below is a compact template sheet you can actually memorize.

# 0. First: the pattern-recognition map

When you see a problem, ask roughly in this order:

| Signal in problem | First thing to consider |
|---|---|
| Need fast lookup / duplicates / frequency / complement | **Hash map / set** |
| Sorted array + pair/triplet | **Two pointers** |
| Contiguous subarray/substring | **Sliding window / prefix sum / Kadane** |
| Need max/min contiguous sum | **Kadane** |
| Nested / matching / "most recent unresolved" | **Stack** |
| Process in arrival/order/layers | **Queue / BFS** |
| Repeatedly need smallest/largest/top K | **Heap / priority queue** |
| Generate **all** combinations/permutations/choices | **Backtracking** |
| Hierarchical parent-child structure | **Tree DFS/BFS** |
| Nodes + arbitrary connections | **Graph DFS/BFS** |
| Connectivity / merging groups | **Union-Find / DSU** |
| Dependencies / prerequisites | **Topological sort** |
| Search sorted/monotonic space | **Binary search** |
| "Best answer" built from overlapping subproblems | **DP** |
| Interval overlap / scheduling | **Sort + intervals / heap** |
| Range sum/count repeatedly | **Prefix sum** |
| Need nearest greater/smaller | **Monotonic stack** |

The bolded wording is what I'd train yourself to notice.

---

# 1. Two pointers — opposite ends

### Recognition

Think:

> **Sorted + pair**  
> **Compare two ends**  
> **Can moving one pointer eliminate possibilities?**

Examples: Two Sum II, 3Sum, palindrome, Container With Most Water.

### Template

```python
left, right = 0, len(nums) - 1

while left < right:
    if condition(nums[left], nums[right]):
        # found answer
        return ...

    elif need_larger:
        left += 1

    else:
        right -= 1
```

For target sum:

```python
while left < right:
    s = nums[left] + nums[right]

    if s == target:
        return [left, right]
    elif s < target:
        left += 1
    else:
        right -= 1
```

Mental model:

```text
too small → increase left
too large → decrease right
```

---

# 2. Two pointers — read/write

### Recognition

> Modify array **in place**  
> Remove/filter/move elements  
> Keep valid elements together

Examples: Remove Duplicates, Move Zeroes, Remove Element.

### Template

```python
write = 0

for read in range(len(nums)):
    if should_keep(nums[read]):
        nums[write] = nums[read]
        write += 1

return write
```

Think:

```text
read  = explorer
write = where valid stuff goes
```

---

# 3. Fast/slow pointers

### Recognition

Usually linked lists:

> cycle?  
> middle?  
> two pointers moving at different speeds?

### Template

```python
slow = fast = head

while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
```

For cycle:

```python
if slow == fast:
    return True
```

---

# 4. Sliding window

This is arguably the most important extension of two pointers.

### Recognition

Look for:

> **contiguous** subarray / substring  
> longest/shortest substring satisfying X  
> at most K ___  
> no duplicates  
> window

### Template

```python
left = 0

for right in range(len(nums)):
    # add nums[right]

    while window_invalid:
        # remove nums[left]
        left += 1

    # update answer
```

Example:

```python
left = 0
seen = set()
ans = 0

for right in range(len(s)):

    while s[right] in seen:
        seen.remove(s[left])
        left += 1

    seen.add(s[right])
    ans = max(ans, right - left + 1)
```

Mental model:

> **R expands → invalid? → L shrinks → record answer**

---

# 5. Hash map / set

### Recognition

The strongest signals:

> Have I seen X before?  
> How many times have I seen X?  
> Find complement  
> duplicates  
> frequency  
> map one thing → another

### Basic template

```python
seen = {}

for i, x in enumerate(nums):
    if x in seen:
        ...
    
    seen[x] = i
```

### Two Sum template

```python
seen = {}

for i, x in enumerate(nums):
    complement = target - x

    if complement in seen:
        return [seen[complement], i]

    seen[x] = i
```

### Frequency template

```python
count = {}

for x in nums:
    count[x] = count.get(x, 0) + 1
```

Or:

```python
from collections import Counter

count = Counter(nums)
```

Mental trigger:

> **I need O(1)-ish information about something I've already encountered.**

---

# 6. Kadane's algorithm

This is worth memorizing because it's tiny.

### Recognition

The giant signal:

> **maximum sum contiguous subarray**

Example:

```text
[-2,1,-3,4,-1,2,1,-5,4]

             └───────┘
              4,-1,2,1 = 6
```

### Template

```python
current = best = nums[0]

for x in nums[1:]:
    current = max(x, current + x)
    best = max(best, current)

return best
```

The entire algorithm comes from one question:

> At `x`, should I **continue my previous subarray** or **start fresh at x**?

Therefore:

```python
current = max(
    x,            # start fresh
    current + x   # continue
)
```

That's Kadane.

### Pattern

```text
maximum
+ contiguous
+ sum
        ↓
     KADANE
```

It's essentially a tiny DP:

\[
dp[i]=\max(nums[i],dp[i-1]+nums[i])
\]

---

# 7. Prefix sum

This is another major one you were missing.

### Recognition

> sum of range `[L:R]`  
> many range-sum queries  
> subarray sum = K  
> cumulative counts

### Template

```python
prefix = [0]

for x in nums:
    prefix.append(prefix[-1] + x)
```

Then:

```python
sum(L...R) = prefix[R + 1] - prefix[L]
```

Mental picture:

```text
prefix[R+1]
|----------------|

prefix[L]
|------|

difference
       |---------|
          L..R
```

Very important combo:

> **Prefix sum + hashmap**

for problems like Subarray Sum Equals K.

---

# 8. Stack

### Recognition

Think:

> Last thing opened must close first  
> nested structure  
> undo  
> matching brackets  
> nearest previous/next thing

LIFO:

```text
push A
push B
push C

pop → C
```

### Template

```python
stack = []

for x in items:
    if should_push(x):
        stack.append(x)

    else:
        top = stack.pop()
```

Classic parentheses:

```python
stack = []

for c in s:
    if c in "([{":
        stack.append(c)
    else:
        if not stack:
            return False

        top = stack.pop()
        ...
```

---

# 9. Monotonic stack

Extremely interview-worthy.

### Recognition

Look for:

> next greater element  
> next smaller element  
> previous greater/smaller  
> nearest greater/smaller  
> temperatures / histogram

### Template

```python
stack = []

for i, x in enumerate(nums):

    while stack and nums[stack[-1]] < x:
        j = stack.pop()
        # x is next greater element for nums[j]

    stack.append(i)
```

Mental model:

> Stack contains **unresolved candidates**.

When `x` arrives, it resolves some of them.

---

# 10. Queue

FIFO:

```text
A → B → C

remove → A
```

### Recognition

> process things in arrival order  
> level-by-level  
> BFS  
> shortest path in **unweighted** graph

### Template

```python
from collections import deque

q = deque([start])

while q:
    node = q.popleft()

    for nei in neighbors(node):
        q.append(nei)
```

---

# 11. Priority queue / heap

### Recognition

Huge trigger:

> Repeatedly give me the **smallest/largest** thing.

Also:

> top K  
> kth largest  
> merge K sorted lists  
> scheduling  
> shortest path with weighted edges

### Template

```python
import heapq

heap = []

heapq.heappush(heap, value)

smallest = heapq.heappop(heap)
```

For `(priority, item)`:

```python
heapq.heappush(heap, (priority, item))

priority, item = heapq.heappop(heap)
```

Mental difference:

```text
Queue:
"who arrived first?"

Heap:
"who has highest priority?"
```

---

# 12. Backtracking

### Recognition

This one has very strong wording:

> Generate **all** combinations  
> all permutations  
> all subsets  
> all possible arrangements  
> find all valid configurations

You're exploring a **decision tree**.

```text
                []
           /     |      \
          A      B       C
        /  \    / \     / \
       ...
```

### Universal template

```python
result = []

def backtrack(path, choices):

    if base_case:
        result.append(path.copy())
        return

    for choice in choices:

        # choose
        path.append(choice)

        # explore
        backtrack(path, new_choices)

        # undo
        path.pop()
```

Memorize:

> **Choose → Explore → Undo**

That's backtracking.

---

# 13. Tree DFS

### Recognition

> hierarchy  
> parent/children  
> subtree  
> depth  
> path from root  
> leaf

### Recursive template

```python
def dfs(node):
    if not node:
        return

    dfs(node.left)
    dfs(node.right)
```

But interview problems usually need information returned upward:

```python
def dfs(node):
    if not node:
        return base_value

    left = dfs(node.left)
    right = dfs(node.right)

    return combine(node, left, right)
```

This is the **most important tree template**.

Example height:

```python
def height(node):
    if not node:
        return 0

    left = height(node.left)
    right = height(node.right)

    return 1 + max(left, right)
```

Mental model:

> **Ask children for information → combine it → return to parent.**

---

# 14. Tree BFS

### Recognition

> level order  
> closest level  
> minimum depth  
> process layer-by-layer

### Template

```python
from collections import deque

q = deque([root])

while q:

    for _ in range(len(q)):
        node = q.popleft()

        if node.left:
            q.append(node.left)

        if node.right:
            q.append(node.right)
```

The:

```python
for _ in range(len(q)):
```

trick means:

> process exactly **one level**.

---

# 15. Graph DFS

### Recognition

> nodes connected arbitrarily  
> explore component  
> does path exist?  
> islands  
> connected regions

### Template

```python
visited = set()

def dfs(node):
    if node in visited:
        return

    visited.add(node)

    for nei in graph[node]:
        dfs(nei)
```

The crucial difference from trees:

### Tree

Usually:

```python
no visited needed
```

because parent-child structure prevents arbitrary cycles.

### Graph

Usually:

```python
visited = set()
```

because:

```text
A → B
↑   ↓
D ← C
```

could loop forever.

---

# 16. Graph BFS

### Recognition

The biggest signal:

> **Shortest path in an UNWEIGHTED graph**

Why?

BFS explores:

```text
distance 0
    ↓
distance 1
    ↓
distance 2
    ↓
distance 3
```

Therefore the first time you reach something = shortest number of edges.

### Template

```python
from collections import deque

q = deque([start])
visited = {start}

while q:
    node = q.popleft()

    for nei in graph[node]:
        if nei not in visited:
            visited.add(nei)
            q.append(nei)
```

---

# 17. Topological sort

Another major pattern you should know.

### Recognition

Look for:

> prerequisites  
> dependencies  
> must happen before  
> course schedule  
> build order

That's a **directed acyclic graph** problem.

### Template — Kahn's algorithm

```python
from collections import deque

indegree = [0] * n

for u in range(n):
    for v in graph[u]:
        indegree[v] += 1

q = deque()

for i in range(n):
    if indegree[i] == 0:
        q.append(i)

order = []

while q:
    u = q.popleft()
    order.append(u)

    for v in graph[u]:
        indegree[v] -= 1

        if indegree[v] == 0:
            q.append(v)
```

Mental model:

> Start with things having **zero prerequisites** → remove them → new things become available.

---

# 18. Disjoint Set / Union-Find

This sounds scary but does one basic thing:

> **Are A and B in the same group?**

and:

> **Merge A's group and B's group.**

### Recognition

Look for:

> connectivity  
> groups/components  
> dynamically connecting nodes  
> redundant edge  
> are these connected?  
> merge sets

### Template

```python
parent = list(range(n))
rank = [0] * n

def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])
    return parent[x]

def union(a, b):
    ra = find(a)
    rb = find(b)

    if ra == rb:
        return False

    if rank[ra] < rank[rb]:
        parent[ra] = rb

    elif rank[ra] > rank[rb]:
        parent[rb] = ra

    else:
        parent[rb] = ra
        rank[ra] += 1

    return True
```

Then:

```python
find(a) == find(b)
```

means they're connected.

### DFS vs Union-Find?

Think:

```text
"Explore this component"
        ↓
       DFS

"Keep merging groups and asking
 whether two things are connected"
        ↓
    Union-Find
```

---

# 19. Binary search

Absolutely include this.

### Recognition

Not just:

> sorted array

More generally:

> **monotonic search space**

Something like:

```text
False False False False True True True
                        ↑
                  find boundary
```

### Exact target template

```python
left, right = 0, len(nums) - 1

while left <= right:
    mid = (left + right) // 2

    if nums[mid] == target:
        return mid
    elif nums[mid] < target:
        left = mid + 1
    else:
        right = mid - 1
```

### Boundary / binary search on answer

```python
left, right = min_answer, max_answer

while left < right:
    mid = (left + right) // 2

    if feasible(mid):
        right = mid
    else:
        left = mid + 1

return left
```

The second version is extremely powerful.

Trigger words:

> minimum possible X  
> maximum X such that...  
> smallest capacity  
> earliest time

Ask:

> **If X works, does every larger X also work?**

If yes → binary search may work.

---

# 20. Intervals

Also important.

### Recognition

> meetings  
> schedules  
> overlapping ranges  
> merge ranges  
> rooms/resources

Usually:

> **Sort by start time first.**

### Merge template

```python
intervals.sort()

merged = []

for start, end in intervals:

    if not merged or start > merged[-1][1]:
        merged.append([start, end])

    else:
        merged[-1][1] = max(merged[-1][1], end)
```

---

# 21. Dynamic programming

This is the broadest one and usually the hardest to recognize.

### Recognition

Think DP when:

> Find **maximum/minimum/number of ways**  
> many choices  
> same smaller problems repeat  
> answer can be constructed from previous answers

The core question:

> **What information about the past do I need to make the next decision?**

### 1D template

```python
dp = [0] * n

dp[0] = ...

for i in range(1, n):
    dp[i] = transition(dp[i - 1], ...)

return dp[-1]
```

Or often:

```python
prev = ...

for x in nums:
    curr = transition(prev, x)
    prev = curr
```

Kadane is DP:

```python
dp[i] = max(
    nums[i],
    dp[i - 1] + nums[i]
)
```

House Robber:

```python
dp[i] = max(
    dp[i - 1],              # don't rob
    dp[i - 2] + nums[i]     # rob
)
```

That structure:

```text
choice A → previous state
choice B → another previous state

take best
```

is classic DP.

---

# 22. Backtracking vs DP

This distinction causes a lot of confusion.

If the question says:

> **Give me ALL solutions**

think:

```text
Backtracking
```

If it says:

> **Give me the optimal value / count**

and subproblems repeat:

```text
DP
```

Example:

```text
Generate every subset
→ backtracking

Maximum-value subset under constraints
→ potentially DP
```

---

# 23. BFS vs DFS vs Union-Find

This is another distinction worth memorizing.

### DFS

Think:

> **Explore everything connected to this thing.**

```text
islands
components
path existence
subtree
```

### BFS

Think:

> **Explore outward by distance.**

```text
shortest unweighted path
minimum moves
levels
```

### Union-Find

Think:

> **Maintain groups as connections are added.**

```text
are A/B connected?
merge groups
detect redundant connection
```

---

# 24. Queue vs Heap vs Stack

A very clean way to distinguish them:

```text
STACK
"Give me the MOST RECENT thing."
LIFO

QUEUE
"Give me the OLDEST thing."
FIFO

HEAP
"Give me the MOST IMPORTANT
(smallest/largest priority) thing."
```

So:

```text
nested parentheses
→ stack

BFS
→ queue

top K / repeatedly smallest
→ heap
```

---

# 25. The master interview decision tree

I'd actually memorize something close to this:

```text
Read problem
   │
   ├── Need lookup/count/seen/complement?
   │       → HASHMAP / SET
   │
   ├── Array is sorted?
   │       ├── pair relationship → TWO POINTERS
   │       └── search/boundary → BINARY SEARCH
   │
   ├── Contiguous subarray/string?
   │       ├── longest/shortest constraint → SLIDING WINDOW
   │       ├── max sum → KADANE
   │       └── range/subarray sum → PREFIX SUM
   │
   ├── Generate all possibilities?
   │       → BACKTRACKING
   │
   ├── Parent/child hierarchy?
   │       → TREE DFS/BFS
   │
   ├── Arbitrary connections?
   │       ├── shortest unweighted → BFS
   │       ├── explore/connectivity → DFS
   │       └── repeatedly merge groups → UNION-FIND
   │
   ├── Dependencies/prerequisites?
   │       → TOPOLOGICAL SORT
   │
   ├── Need smallest/largest repeatedly?
   │       → HEAP
   │
   ├── Nested / matching / nearest greater?
   │       → STACK
   │
   ├── Overlapping ranges?
   │       → SORT + INTERVALS
   │
   └── Optimal/count + repeated subproblems?
           → DP
```

## The patterns I'd prioritize for your interview

You don't need 50 algorithms floating independently in your head. I'd make sure you're comfortable with these **15 families**:

1. **Hash map / set**
2. **Two pointers**
3. **Sliding window**
4. **Prefix sum**
5. **Kadane**
6. **Binary search**
7. **Stack / monotonic stack**
8. **Queue / BFS**
9. **Heap / priority queue**
10. **Linked list fast/slow**
11. **Tree DFS/BFS**
12. **Graph DFS/BFS**
13. **Backtracking**
14. **Union-Find + topological sort**
15. **Basic 1D/2D DP**

The most useful habit during an interview is to literally say: **"What structure in this problem lets me eliminate work?"** Sortedness suggests two pointers/binary search; repeated lookup suggests hashing; contiguity suggests windows/prefix sums; hierarchy suggests traversal; choices with repeated states suggest DP. That gets you much farther than trying to remember which LeetCode problem this resembles.


# Python Interview Syntax + Tricks Cheat Sheet

## 1. The absolute basics I would memorize

```python
# loop through values
for x in nums:
    ...

# loop through indices
for i in range(len(nums)):
    ...

# index + value
for i, x in enumerate(nums):
    ...

# two arrays together
for x, y in zip(a, b):
    ...

# backwards
for i in range(n - 1, -1, -1):
    ...

# range [0, n)
range(n)

# range [a, b)
range(a, b)

# range with step
range(a, b, step)
```

Remember: **Python ranges exclude the endpoint.**

```python
list(range(2, 6))
# [2, 3, 4, 5]
```

---

# 2. List tricks

```python
nums = [3, 1, 4, 1, 5]

len(nums)
sum(nums)
min(nums)
max(nums)

nums.append(6)
nums.pop()             # remove last
nums.pop(i)            # remove index i

nums.reverse()         # modifies nums
nums[::-1]             # returns reversed copy

nums.sort()            # modifies nums
sorted(nums)           # returns sorted copy
```

### Slicing

Extremely useful:

```python
nums[l:r]       # l ... r-1
nums[:r]        # beginning ... r-1
nums[l:]        # l ... end
nums[::-1]      # reverse
```

Example:

```python
nums = [10, 20, 30, 40, 50]

nums[1:4]
# [20, 30, 40]
```

---

# 3. List comprehensions

Instead of:

```python
result = []

for x in nums:
    result.append(x * x)
```

write:

```python
result = [x * x for x in nums]
```

Filtering:

```python
evens = [x for x in nums if x % 2 == 0]
```

2D:

```python
grid = [[0] * m for _ in range(n)]
```

⚠️ Don't do:

```python
grid = [[0] * m] * n
```

because the rows refer to the **same list**.

---

# 4. Strings

```python
s = "hello"

s[0]
s[-1]
s[::-1]

s.lower()
s.upper()

s.isalpha()
s.isdigit()
s.isalnum()

s.startswith("he")
s.endswith("lo")
```

Split:

```python
"hello world".split()
# ["hello", "world"]

"a,b,c".split(",")
# ["a", "b", "c"]
```

Join:

```python
" ".join(["hello", "world"])
# "hello world"

"".join(["a", "b", "c"])
# "abc"
```

Convert:

```python
str(123)
int("123")
```

---

# 5. Hash set

Use when you only care:

> **Have I seen this?**

```python
seen = set()

seen.add(x)

if x in seen:
    ...

seen.remove(x)
```

Safer removal:

```python
seen.discard(x)
```

because `discard()` doesn't error if `x` isn't present.

### Remove duplicates

```python
unique = set(nums)
```

### Intersection

```python
a & b
```

Union:

```python
a | b
```

Difference:

```python
a - b
```

Very handy.

---

# 6. Dictionary / hashmap

```python
d = {}

d[key] = value

if key in d:
    ...

value = d[key]
```

Safer:

```python
d.get(key, default)
```

Classic frequency count:

```python
count = {}

for x in nums:
    count[x] = count.get(x, 0) + 1
```

Loop:

```python
for key, value in d.items():
    ...
```

Just keys:

```python
for key in d:
    ...
```

---

# 7. Counter — VERY useful

```python
from collections import Counter

count = Counter(nums)
```

Example:

```python
Counter([1, 1, 1, 2, 2, 3])

# {1: 3, 2: 2, 3: 1}
```

Then:

```python
count[1]
# 3
```

Most common:

```python
count.most_common(2)
```

Great for:

- frequencies
- anagrams
- majority/count problems
- probability simulations

---

# 8. defaultdict

Instead of:

```python
if key not in d:
    d[key] = []

d[key].append(x)
```

do:

```python
from collections import defaultdict

d = defaultdict(list)

d[key].append(x)
```

Common versions:

```python
defaultdict(list)
defaultdict(int)
defaultdict(set)
```

Very useful for **graphs**:

```python
graph = defaultdict(list)

for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)
```

---

# 9. Queue / deque

Never do this repeatedly:

```python
arr.pop(0)   # O(n)
```

Use:

```python
from collections import deque

q = deque()

q.append(x)
x = q.popleft()
```

BFS:

```python
q = deque([start])

while q:
    node = q.popleft()
```

Deque also supports both ends:

```python
q.append(x)
q.appendleft(x)

q.pop()
q.popleft()
```

---

# 10. Stack

Python list works perfectly:

```python
stack = []

stack.append(x)    # push
x = stack.pop()    # pop

stack[-1]          # peek
```

Always be careful:

```python
if stack:
    top = stack[-1]
```

rather than indexing an empty stack.

---

# 11. Heap / priority queue

Python's `heapq` is a **min heap**.

```python
import heapq

heap = []

heapq.heappush(heap, x)
x = heapq.heappop(heap)
```

Turn list into heap:

```python
heapq.heapify(nums)
```

### Max heap classic interview trick

Negate numbers:

```python
heapq.heappush(heap, -x)

largest = -heapq.heappop(heap)
```

### Priority + object

```python
heapq.heappush(heap, (distance, node))

distance, node = heapq.heappop(heap)
```

Tuples are compared lexicographically.

---

# 12. Sorting tricks

Basic:

```python
nums.sort()
```

Descending:

```python
nums.sort(reverse=True)
```

Sort tuples by second value:

```python
items.sort(key=lambda x: x[1])
```

Sort by first ascending, second descending:

```python
items.sort(key=lambda x: (x[0], -x[1]))
```

Very useful for intervals:

```python
intervals.sort(key=lambda x: x[0])
```

---

# 13. `enumerate()` and `zip()` — memorize these

Instead of:

```python
for i in range(len(nums)):
    print(i, nums[i])
```

use:

```python
for i, x in enumerate(nums):
    print(i, x)
```

Two lists:

```python
for x, y in zip(a, b):
    ...
```

Example:

```python
names = ["A", "B"]
scores = [90, 80]

for name, score in zip(names, scores):
    ...
```

---

# 14. `any()` and `all()`

These are underrated.

```python
any(x > 10 for x in nums)
```

means:

> Is **at least one** > 10?

While:

```python
all(x > 10 for x in nums)
```

means:

> Are **all** > 10?

Probability analogy:

```text
any  ≈ ∃
all  ≈ ∀
```

---

# 15. `min()` / `max()` with key

Suppose:

```python
people = [
    ("Alice", 25),
    ("Bob", 32),
    ("Carol", 28)
]
```

Oldest:

```python
max(people, key=lambda x: x[1])
```

Returns:

```python
("Bob", 32)
```

Extremely useful when objects/tuples contain scores.

---

# 16. Infinity

Instead of some giant arbitrary number:

```python
best = 999999999
```

use:

```python
best = float("inf")
```

or:

```python
best = float("-inf")
```

Common:

```python
minimum = float("inf")
maximum = float("-inf")
```

---

# 17. Swap variables

Don't do temporary-variable nonsense:

```python
temp = a
a = b
b = temp
```

Python:

```python
a, b = b, a
```

---

# 18. Multiple assignment

Very useful:

```python
left, right = 0, n - 1

row, col = 0, 0

slow = fast = head
```

Unpacking:

```python
a, b = [10, 20]
```

---

# 19. Useful math functions

```python
import math
```

### Ceiling / floor

```python
math.ceil(3.2)     # 4
math.floor(3.8)    # 3
```

### Square root

```python
math.sqrt(x)
```

Often better:

```python
x ** 0.5
```

### GCD

```python
math.gcd(a, b)
```

### Factorial

```python
math.factorial(n)
```

This is especially useful for probability/combinatorics.

---

# 20. Probability: combinations and permutations

You should absolutely know these Python functions.

```python
import math

math.comb(n, k)
```

calculates:

\[
{n \choose k}
\]

Example:

```python
math.comb(52, 5)
```

Number of 5-card hands.

### Permutations

```python
math.perm(n, k)
```

calculates:

\[
P(n,k)=\frac{n!}{(n-k)!}
\]

The conceptual distinction:

```text
Choose k people for a committee
→ comb(n, k)
→ ORDER DOESN'T MATTER

Choose president, VP, secretary
→ perm(n, 3)
→ ORDER/ROLE MATTERS
```

This distinction is much more important than memorizing the formulas.

---

# 21. Generate combinations/permutations in Python

For debugging/small cases:

```python
from itertools import combinations

list(combinations([1,2,3], 2))
```

gives:

```text
(1,2)
(1,3)
(2,3)
```

Permutations:

```python
from itertools import permutations

list(permutations([1,2,3]))
```

Cartesian product:

```python
from itertools import product

list(product([0,1], repeat=3))
```

gives all:

```text
000
001
010
011
100
101
110
111
```

This is **very useful for checking probability answers** on tiny sample spaces.

---

# 22. Probability simulation

If you're unsure about an answer, simulate it.

```python
import random

random.random()
```

uniform between 0 and 1.

Random integer:

```python
random.randint(1, 6)
```

Simulate dice:

```python
trials = 100000
success = 0

for _ in range(trials):
    a = random.randint(1, 6)
    b = random.randint(1, 6)

    if a + b == 7:
        success += 1

print(success / trials)
```

Should approach:

\[
\frac16.
\]

This obviously isn't your mathematical proof, but it's a fantastic **sanity check**.

---

# 23. Probability: exact enumeration

Often better than simulation for tiny spaces.

Two dice:

```python
from itertools import product

outcomes = list(product(range(1, 7), repeat=2))

good = sum(a + b == 7 for a, b in outcomes)

prob = good / len(outcomes)
```

Notice this trick:

```python
sum(condition for ...)
```

because Python treats:

```python
True  == 1
False == 0
```

So:

```python
sum(x > 5 for x in nums)
```

counts how many values exceed 5.

Very useful.

---

# 24. Probability: random choices

Random element:

```python
random.choice(items)
```

Sample **without replacement**:

```python
random.sample(items, k)
```

Example:

```python
hand = random.sample(deck, 5)
```

Sample **with replacement**:

```python
random.choices(items, k=5)
```

Important distinction for probability questions.

---

# 25. Useful probability formulas to translate directly into code

### Binomial coefficient

```python
math.comb(n, k)
```

### Binomial probability

\[
P(X=k)=
{n\choose k}p^k(1-p)^{n-k}
\]

Code:

```python
prob = (
    math.comb(n, k)
    * p**k
    * (1-p)**(n-k)
)
```

### At least one

Almost always think complement:

\[
P(X\ge1)=1-P(X=0)
\]

Code:

```python
p_at_least_one = 1 - p_none
```

This is one of the biggest probability interview tricks.

---

# 26. Expectation trick

For a discrete variable:

\[
E[X]=\sum_x xP(X=x)
\]

Python:

```python
expected = sum(x * p for x, p in outcomes)
```

But in interviews, remember the much more important trick:

## Indicator variables

If:

\[
X = I_1 + I_2+\cdots+I_n
\]

then:

\[
E[X]
=
\sum_i E[I_i]
=
\sum_i P(I_i=1)
\]

**Independence is NOT required.**

So whenever the question says:

> expected number of ___

strongly consider:

> **Define one indicator for each thing.**

This is one of the highest-value probability interview patterns.

---

# 27. `sum()` tricks

You can do:

```python
sum(nums)
```

but also:

```python
sum(x * x for x in nums)
```

Count condition:

```python
sum(x > 5 for x in nums)
```

Count evens:

```python
sum(x % 2 == 0 for x in nums)
```

Probability enumeration:

```python
good = sum(a + b == 7 for a, b in outcomes)
```

Very handy.

---

# 28. `min/max` trick for algorithms

Kadane:

```python
current = max(x, current + x)
```

DP:

```python
dp[i] = max(
    dp[i - 1],
    dp[i - 2] + nums[i]
)
```

Clamping:

```python
x = max(x, 0)
```

Clamp to interval:

```python
x = max(low, min(x, high))
```

---

# 29. Modulo `%` tricks

### Even/odd

```python
x % 2 == 0
```

### Circular array

Instead of running off the end:

```python
next_i = (i + 1) % n
```

Example:

```text
0 → 1 → 2 → 3 → 0
```

### Last digit

```python
x % 10
```

### Remove last digit

```python
x // 10
```

---

# 30. Integer division

This trips people up.

```python
7 / 2
# 3.5
```

but:

```python
7 // 2
# 3
```

Binary search:

```python
mid = (left + right) // 2
```

---

# 31. Digits of a number

Extract last digit:

```python
digit = n % 10
n //= 10
```

Or often easier:

```python
for digit in str(n):
    ...
```

Convert back:

```python
int(digit)
```

---

# 32. Character ↔ integer tricks

ASCII/Unicode:

```python
ord('a')       # 97
chr(97)        # 'a'
```

Useful for letter indexing:

```python
index = ord(c) - ord('a')
```

Then:

```text
a → 0
b → 1
...
z → 25
```

Useful for frequency arrays:

```python
count = [0] * 26

for c in s:
    count[ord(c) - ord('a')] += 1
```

---

# 33. Matrix/grid traversal

You'll see this constantly.

```python
rows = len(grid)
cols = len(grid[0])
```

Four directions:

```python
directions = [
    (1, 0),
    (-1, 0),
    (0, 1),
    (0, -1)
]
```

Then:

```python
for dr, dc in directions:
    nr = r + dr
    nc = c + dc

    if 0 <= nr < rows and 0 <= nc < cols:
        ...
```

Memorize that.

It's the basis of:

- Number of Islands
- flood fill
- grid BFS
- grid DFS
- shortest path

---

# 34. Graph construction

Given:

```python
edges = [
    (0, 1),
    (0, 2),
    (1, 3)
]
```

Undirected:

```python
from collections import defaultdict

graph = defaultdict(list)

for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)
```

Directed:

```python
for u, v in edges:
    graph[u].append(v)
```

Only add the reverse edge if it's undirected.

---

# 35. Recursion skeleton

```python
def dfs(node):
    # base case
    if ...:
        return ...

    # recursive work
    result = dfs(...)

    # combine
    return ...
```

For trees:

```python
def dfs(node):
    if not node:
        return 0

    left = dfs(node.left)
    right = dfs(node.right)

    return 1 + max(left, right)
```

---

# 36. Backtracking skeleton

Memorize these three words:

> **Choose → Explore → Undo**

```python
def backtrack(path):

    if complete:
        result.append(path.copy())
        return

    for choice in choices:

        path.append(choice)   # choose

        backtrack(path)       # explore

        path.pop()            # undo
```

⚠️ Notice:

```python
result.append(path.copy())
```

not:

```python
result.append(path)
```

because `path` keeps changing.

Very common bug.

---

# 37. Memoization

If recursion keeps solving the same thing:

```python
from functools import cache

@cache
def dp(i):
    if base_case:
        return ...

    return ...
```

This is extremely useful in interviews.

Example Fibonacci:

```python
from functools import cache

@cache
def fib(n):
    if n <= 1:
        return n

    return fib(n - 1) + fib(n - 2)
```

Turns exponential repeated work into O(n).

---

# 38. `sorted()` + lambda

This syntax looks ugly until you get used to it:

```python
sorted(items, key=lambda x: x[1])
```

Read it as:

> Sort each `x` according to `x[1]`.

For:

```python
items = [
    ("A", 5),
    ("B", 2),
    ("C", 8)
]
```

you get order:

```text
B, A, C
```

because:

```text
2 < 5 < 8
```

---

# 39. Useful tuple trick

Tuples naturally compare left → right.

```python
(1, 5) < (2, 0)
# True
```

because Python first compares `1` vs `2`.

This is why heaps often use:

```python
(priority, node)
```

and sorting intervals naturally works:

```python
intervals.sort()
```

It sorts by start first, then end.

---

# 40. `bisect` — built-in binary search

Useful to know, although in an interview I'd still be able to implement binary search.

```python
import bisect
```

Insertion position:

```python
i = bisect.bisect_left(nums, target)
```

First position where target could go.

```python
i = bisect.bisect_right(nums, target)
```

position **after** existing targets.

Example:

```text
nums = [1, 2, 2, 2, 5]

bisect_left(nums, 2)
              ↓
[1, 2, 2, 2, 5]
    ^ index 1

bisect_right(nums, 2)
                    ^
                  index 4
```

---

# 41. Common complexity facts to know instantly

You should be able to say these without thinking:

| Operation | Complexity |
|---|---:|
| `arr[i]` | O(1) |
| `arr.append()` | amortized O(1) |
| `arr.pop()` | O(1) |
| `arr.pop(0)` | **O(n)** |
| `x in list` | O(n) |
| `x in set` | avg O(1) |
| `x in dict` | avg O(1) |
| sort | O(n log n) |
| heap push/pop | O(log n) |
| deque `popleft()` | O(1) |
| binary search | O(log n) |
| DFS/BFS | O(V + E) |

A very common optimization is literally:

```text
"x in list"     O(n)

        ↓

"x in set"      O(1) average
```

---

# 42. Probability pattern recognition

Since you're preparing probability alongside coding, I'd memorize this separately:

| Wording | Think |
|---|---|
| "at least one" | **Complement: `1 - P(none)`** |
| "given that..." | **Conditional probability** |
| reverse conditional | **Bayes** |
| choose objects, order irrelevant | **Combination** |
| arrange/order objects | **Permutation** |
| repeated independent success/failure | **Binomial** |
| number of events in time/space | **Poisson** |
| waiting time until event | **Exponential** |
| expected number of X | **Indicator variables + linearity** |
| expected time until success | **Geometric / recursion** |
| min of independent exponentials | **Rates add** |
| probability of union | **Inclusion-exclusion** |
| "random permutation" | **symmetry** |
| complicated finite outcomes | **Condition / count sample space** |
| unsure whether answer makes sense | **enumerate small case / simulate** |

And one more meta-trick:

> Before doing algebra, ask whether **symmetry, complement, conditioning, or linearity of expectation** makes the problem trivial.

Those four tricks kill a surprising number of probability interview questions.

---

# 43. My "coding interview scratchpad"

If I were walking into your interview, these are the imports I'd want in muscle memory:

```python
from collections import Counter, defaultdict, deque
from functools import cache
from itertools import combinations, permutations, product

import heapq
import math
import random
import bisect
```

And know immediately:

```python
Counter(...)                 # frequency
defaultdict(list)            # graph/grouping
deque(...)                   # BFS

heapq.heappush(...)
heapq.heappop(...)           # priority queue

math.comb(n, k)              # n choose k
math.perm(n, k)              # permutations
math.factorial(n)

combinations(...)
permutations(...)
product(...)

@cache                       # memoization

bisect.bisect_left(...)      # binary search boundary
```

## One final distinction that saves a lot of time

When you're stuck, classify **what operation you wish were cheap**:

```text
"I wish I could instantly know whether I've seen x."
→ SET

"I wish I could instantly get information associated with x."
→ HASHMAP

"I wish I could repeatedly get the smallest/largest."
→ HEAP

"I wish I could remove the oldest thing."
→ QUEUE

"I wish I could access the most recently unresolved thing."
→ STACK

"I wish I could eliminate half the possibilities."
→ BINARY SEARCH

"I wish I didn't have to reconsider every pair."
→ TWO POINTERS / HASHMAP

"I keep recomputing the same state."
→ DP / MEMOIZATION
```

That way you're choosing the **data structure based on the operation you need**, rather than trying to pattern-match against a memorized LeetCode title.