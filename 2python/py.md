# 🐍 Python Functions Cheat Sheet — Data Engineer Interviews
---
## 1. Essential Built-in Functions

```python
# --- Sorting ---
sorted([3,1,2]) # → [1, 2, 3] (returns new list)
sorted(data, key=lambda x: x[1]) # sort by second element
sorted(data, key=lambda x: x[1], reverse=True)
list.sort() # in-place sort, returns None

# --- Min / Max ---
min(iterable)
max(iterable, key=lambda x: x['age']) # max by custom key
min(a, b, c) # works with multiple args too

# --- Map / Filter / Reduce ---
list(map(str, [1,2,3])) # → ['1','2','3']
list(filter(lambda x: x > 2, [1,2,3,4])) # → [3, 4]
from functools import reduce
reduce(lambda a, b: a + b, [1,2,3,4]) # → 10

# --- Zip / Enumerate ---
list(zip([1,2], ['a','b'])) # → [(1,'a'), (2,'b')]
list(zip(*matrix)) # transpose a matrix!
for i, val in enumerate(lst, start=1): ...

# --- Any / All ---
any([False, True, False]) # → True (at least one)
all([True, True, True]) # → True (every element)

# --- Type Conversions ---
int("42") float("3.14") str(100)
list("abc") # → ['a','b','c']
set([1,2,2,3]) # → {1,2,3}
dict(zip(keys, values))
tuple([1,2,3])

# --- Other Essentials ---
len(obj) abs(-5) sum(iterable)
round(3.14159, 2) # → 3.14
pow(2, 10) # → 1024
divmod(17, 5) # → (3, 2) → (quotient, remainder)
isinstance(x, (int, float))
hash("key") id(obj)
reversed(seq) # returns iterator
```

---
## 2. String Methods

```python
s = " Hello, World! "
# --- Cleaning ---
s.strip() # "Hello, World!"
s.lstrip() # "Hello, World! "
s.rstrip() # " Hello, World!"

# --- Case ---
s.lower() s.upper() s.title() s.capitalize() s.swapcase()

# --- Search ---
s.find("World") # 9 (returns -1 if not found)
s.index("World") # 9 (raises ValueError if not found)
s.count("l") # 3
s.startswith("He") s.endswith("!")

# --- Check Type ---
s.isdigit() s.isalpha() s.isalnum() s.isspace()

# --- Split / Join ---
"a,b,c".split(",") # → ['a','b','c']
"hello world".split() # splits on whitespace
" ".join(["a","b","c"]) # → "a b c"
"a\nb\nc".splitlines() # → ['a','b','c']

# --- Replace ---
s.replace("World", "Python")
s.replace("l", "", 1) # replace only first occurrence

# --- Formatting ---
f"Name: {name}, Age: {age}"
"{:.2f}".format(3.14159) # → "3.14"
"hello".center(20, "-") # "-------hello--------"
"42".zfill(5) # → "00042"

# --- Partition ---
"key=value".partition("=") # → ('key', '=', 'value')
```

---
## 3. List Operations

```python
lst = [1, 2, 3, 4, 5]

# --- Add ---
lst.append(6) # add to end
lst.insert(0, 0) # insert at index
lst.extend([7, 8]) # add multiple items

# --- Remove ---
lst.pop() # remove & return last
lst.pop(0) # remove & return at index
lst.remove(3) # remove first occurrence of value
del lst[1:3] # delete slice

# --- Search ---
lst.index(4) # first index of value
lst.count(2) # count occurrences
4 in lst # membership check → True

# --- Slicing (VERY IMPORTANT) ---
lst[1:4] # elements at index 1,2,3
lst[::2] # every 2nd element
lst[::-1] # reverse the list
lst[-3:] # last 3 elements
lst[:] # shallow copy

# --- Comprehensions ---
[x**2 for x in range(10)]
[x for x in lst if x % 2 == 0]
[x if x > 0 else 0 for x in data] # with else
[[0]*cols for _ in range(rows)] # 2D matrix init

# --- Flatten nested list ---
flat = [item for sub in nested for item in sub]
# --- Copy ---
import copy
shallow = lst.copy() # or lst[:]
deep = copy.deepcopy(nested) # for nested structures
```
---
## 4. Dictionary Operations


```python
d = {"a": 1, "b": 2, "c": 3}

# --- Access ---
d["a"] # KeyError if missing
d.get("z", 0) # returns 0 if missing (safe)

# --- Add / Update ---
d["d"] = 4
d.update({"e": 5, "f": 6})
d |= {"g": 7} # Python 3.9+ merge update

# --- Remove ---
d.pop("a") # remove & return value
d.pop("z", None) # safe remove
del d["b"]
d.popitem() # remove last inserted pair

# --- Iterate ---
d.keys() d.values() d.items()
for k, v in d.items(): ...

# --- Comprehensions ---
{k: v for k, v in d.items() if v > 1}
{v: k for k, v in d.items()} # invert dict
{x: x**2 for x in range(5)}

# --- Merge ---
merged = {**d1, **d2} # d2 overwrites d1
merged = d1 | d2 # Python 3.9+

# --- setdefault (great for grouping) ---
groups = {}
for item in data:
groups.setdefault(item["key"], []).append(item)

# --- Sort dict by value ---
sorted(d.items(), key=lambda x: x[1], reverse=True)
```
---
## 5. Set Operations

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b # union → {1,2,3,4,5,6}
a & b # intersection → {3,4}
a - b # difference → {1,2}
a ^ b # symmetric diff → {1,2,5,6}
a.issubset(b) # False
a.issuperset(b)# False

a.add(5) a.discard(5) # discard won't raise error
a.remove(5) # raises KeyError if missing
```
---
## 6. `collections` Module ⭐

```python
from collections import Counter, defaultdict, deque, OrderedDict, namedtuple

# --- Counter (frequency counting) ---
c = Counter("aabbbcccc") # Counter({'c':4, 'b':3, 'a':2})
c.most_common(2) # [('c',4), ('b',3)]
c["a"] # 2
c.update("aaa") # add counts
Counter(lst1) - Counter(lst2) # difference of counts

# --- defaultdict (auto-initialize) ---
dd = defaultdict(list)
dd["key"].append(1) # no KeyError!
dd = defaultdict(int) # default 0 — great for counting
dd = defaultdict(set)

# --- deque (double-ended queue, O(1) both ends) ---
dq = deque([1, 2, 3])
dq.appendleft(0) dq.append(4)
dq.popleft() dq.pop()
dq.rotate(1) # rotate right
dq = deque(maxlen=3) # auto-evicts oldest when full → sliding window!

# --- OrderedDict ---
od = OrderedDict()
od.move_to_end("key") # move to end
od.move_to_end("key", last=False) # move to front → LRU cache pattern

# --- namedtuple ---
Point = namedtuple("Point", ["x", "y"])
p = Point(1, 2)
p.x, p.y # → 1, 2
```
---
## 7. `itertools` Module ⭐

```python
import itertools
# --- Combinatorics ---
itertools.permutations([1,2,3]) # all orderings (6)
itertools.permutations([1,2,3], 2) # 2-length perms
itertools.combinations([1,2,3], 2) # → (1,2),(1,3),(2,3)
itertools.combinations_with_replacement([1,2,3], 2)
itertools.product([0,1], repeat=3) # cartesian product → 000,001,...

# --- Grouping ---
from itertools import groupby
data = sorted(data, key=lambda x: x["dept"]) # MUST sort first!
for key, group in groupby(data, key=lambda x: x["dept"]):
print(key, list(group))

# --- Chaining ---
itertools.chain([1,2], [3,4]) # → 1,2,3,4
itertools.chain.from_iterable([[1,2],[3,4]])

# --- Infinite Iterators ---
itertools.count(10, 2) # 10, 12, 14, 16, ...
itertools.cycle([1,2,3]) # 1, 2, 3, 1, 2, 3, ...
itertools.repeat("x", 3) # x, x, x

# --- Slicing Iterators ---
itertools.islice(iterable, 5) # first 5
itertools.islice(iterable, 2, 8, 2) # start=2, stop=8, step=2

# --- Accumulate ---
list(itertools.accumulate([1,2,3,4])) # → [1, 3, 6, 10] prefix sums!
list(itertools.accumulate([3,1,4,1], max)) # running max
```
---
## 8. `functools` Module

```python
from functools import reduce, lru_cache, partial, cmp_to_key


# --- reduce ---
reduce(lambda a, b: a * b, [1,2,3,4]) # → 24

# --- lru_cache (memoization for recursion/DP) ---
@lru_cache(maxsize=None)
def fib(n):
	if n < 2: return n
		return fib(n-1) + fib(n-2)

# --- partial (pre-fill arguments) ---
int_base2 = partial(int, base=2)
int_base2("1010") # → 10

# --- cmp_to_key (custom sort comparator) ---
def compare(a, b):
return -1 if a < b else 1 if a > b else 0
sorted(data, key=cmp_to_key(compare))
```

---
## 9. `heapq` — Min-Heap / Priority Queue

```python
import heapq

nums = [5, 1, 3, 7, 2]
heapq.heapify(nums) # in-place → min-heap
heapq.heappush(nums, 0)
smallest = heapq.heappop(nums) # pops smallest
heapq.nlargest(3, nums) # top 3 largest
heapq.nsmallest(3, nums) # top 3 smallest

# --- Max-Heap trick (negate values) ---
heapq.heappush(heap, -val)
max_val = -heapq.heappop(heap)

# --- Heap with tuples (priority, data) ---
heapq.heappush(heap, (priority, data))
# --- Merge sorted iterables ---
list(heapq.merge([1,3,5], [2,4,6])) # → [1,2,3,4,5,6]
```

---
## 10. `bisect` — Binary Search on Sorted Lists

```python
import bisect

arr = [1, 3, 5, 7, 9]
bisect.bisect_left(arr, 5) # → 2 (insert point, left of existing)
bisect.bisect_right(arr, 5) # → 3 (insert point, right of existing)
bisect.insort(arr, 4) # insert maintaining sort order
```

---
## 11. `re` — Regular Expressions

```python
import re

re.match(r"^\d+", s) # match at start only
re.search(r"\d+", s) # first match anywhere
re.findall(r"\d+", s) # all matches → list of strings
re.sub(r"\s+", " ", s) # replace pattern
re.split(r"[,;\s]+", s) # split on pattern


# --- Common Patterns ---
# \d digit \w word char \s whitespace
# . any char ^ start $ end
# + 1 or more * 0 or more ? 0 or 1

# --- Groups ---
m = re.search(r"(\w+)@(\w+)\.(\w+)", email)
m.group(0) # full match
m.group(1) # first group
m.groups() # all groups as tuple
```
---
## 12. `datetime` Module

```python
from datetime import datetime, date, timedelta

now = datetime.now()
today = date.today()

# --- Parsing & Formatting ---
dt = datetime.strptime("2026-05-30", "%Y-%m-%d")
dt.strftime("%d/%m/%Y %H:%M:%S")

# --- Arithmetic ---
tomorrow = today + timedelta(days=1)
diff = date(2026,12,31) - today # → timedelta object
diff.days # number of days

# --- Common Format Codes ---
# %Y year(4) %m month(01-12) %d day(01-31)
# %H hour(24) %M minute %S second
```
---
## 13. File I/O & JSON/CSV

```python
# --- File Read/Write ---
with open("file.txt", "r") as f:
	content = f.read() # entire file as string
	lines = f.readlines() # list of lines
	for line in f: ... # line by line (memory efficient)

with open("file.txt", "w") as f: # "a" for append	
	f.write("hello\n")
	f.writelines(["a\n", "b\n"])

# --- JSON ---
import json

data = json.loads(json_string) # string → dict
json_string = json.dumps(data, indent=2) # dict → string
json.load(file_obj) # file → dict
json.dump(data, file_obj) # dict → file
```

## 14. Generators & Decorators

```python
# --- Generator (memory-efficient iteration) ---
def read_large_file(path):
	with open(path) as f:
		for line in f:
			yield line.strip()

# Generator expression
squares = (x**2 for x in range(10**6)) # lazy, no memory spike

# --- Decorator ---
import time
def timer(func):
	def wrapper(*args, **kwargs):
		start = time.time()
		result = func(*args, **kwargs)
		print(f"{func.__name__} took {time.time()-start:.4f}s")
		return result
	return wrapper
```
---
## 15. Error Handling

```python
try:
	result = risky_operation()
	except (ValueError, KeyError) as e:
	print(f"Error: {e}")

except Exception as e:
	raise # re-raise
else:
	print("Success!") # runs if no exception
finally:
	cleanup() # always runs
```

---


## 16. Quick Reference Table

  

| Task | Function / Pattern |

|---|---|

| Count frequency | `Counter(lst)` |

| Group by key | `defaultdict(list)` / `setdefault` |

| Top K elements | `heapq.nlargest(k, data)` |

| Remove duplicates (keep order) | `list(dict.fromkeys(lst))` |

| Flatten 2D list | `[x for sub in lst for x in sub]` |

| Transpose matrix | `list(zip(*matrix))` |

| Reverse string/list | `s[::-1]` |

| Check palindrome | `s == s[::-1]` |

| Prefix sums | `itertools.accumulate(arr)` |

| Binary search | `bisect.bisect_left(arr, x)` |

| Sliding window | manual loop with add/remove |

| Memoize recursion | `@lru_cache(maxsize=None)` |

| Parse date string | `datetime.strptime(s, fmt)` |

| Merge sorted lists | `heapq.merge(a, b)` |

| Cartesian product | `itertools.product(a, b)` |

| All subsets | `itertools.combinations` |

  

---

  

> **💡 Tip:** In interviews, always clarify edge cases (empty input, duplicates, negatives) before coding. Use `Counter`, `defaultdict`, and `heapq` — they save time and impress interviewers.