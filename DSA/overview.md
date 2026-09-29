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