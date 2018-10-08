## Dataflow
### 1. Chained comparison operators
```python
if x <= y and y <= z:
  print('ok')
```
**Better**
```python
if x <= y <= z:
  # do something
```
___

### 2. Ternary operator
```python
value = 0
if cond:
  value = 1
```
**Better**
```python
value = 1 if cond else 0
```
Intuitively it's like how we write in maths, `f(x) = |x| = x if x > 0 else -x`
___

### 3. `or` operator
```python
if x:
  y = x
else:
  y = 'fallback'
```
**Better**: use `or`
```python
y = x or 'fallback'
```
_`or`_ returns the first operand if the first operand evaluates to True, and the second operand if the first operand evaluates to False. Examples:
```python
'' or 'default' # 'default'
0 or 1 # 1
None or 0 # 0
[] or [3] # [3]
None or [] # []
False or 0 # 0
```
___
___

### Check existence in a collection
```python
if city == 'Nairobi' or city == 'Kampala' or city == 'Lagos':
  found = True
```
**Better**: use `in` keyword
```python
city = 'Nairobi'
found = city in {'Nairobi', 'Kampala', 'Lagos'}
```
Here we used a set of cities, though we could also have used 
1. a tuple, `('Nairobi', 'Kampala', 'Lagos')`, or
2. a list `['Nairobi', 'Kampala', 'Lagos']`

Set will be advantageous when number of cities is very large. In summary, use `in` where possible:
1. _Contains_: `if x in items`
2. _Iteration_: `for x in items`
___

### Concatenating strings
```python
sentence = ['this','is','a','sentence']
sentence_str = ''
for word in sentence:
  sentence_str += word + '  '
sentence_str = sentence_str[:-1]
# 'this is a sentence'
```
Above code uses the [Shlemiel the painter’s algorithm](https://www.joelonsoftware.com/2001/12/11/back-to-basics/) and is accidentaly quadratic 👎. Instead **use `join`**
```python
' '.join(sentence)
```
___

## Looping
### Simple Looping
```python
for i in range(len(my_list)):
  print(my_list[i])
```
**Better** 👇
```python
for elem in my_list:
  print(elem)
```
___

### Looping over a collection with indices
```python
for i in range(len(my_list)):
  print(i, my_list[i])
```
**Better**: use **`enumerate`** :bowtie: 
```python
for idx, element in enumerate(my_list):
  print (idx, element)
```
`enumerate` returns an iterator
___

### Looping backwards
```python
colors = ['red', 'green', 'blue', 'yellow']

for i in range(len(colors)-1, -1, -1):
  print(colors[i])
```
**Better**: use slicing `[::-1]`
```python
for color in colors[::-1]:
  print(color)
```
**Even Better**: use **`reversed`** 👌. It returns an iterator.
```python
for color in reversed(colors):
  print(color)
```
___

### Looping over two collections
```python
names = ['raymond', 'rachel', 'matthew']
colors = ['red', 'green', 'blue', 'yellow']

n = min(len(names), len(colors))
for i in range(n):
  print(names[i], '--->', colors[i])
```
**Better**: use **`zip`**
```python
for name, color in zip(names, colors):
  print(name, '--->', color)
```
`zip` too returns an iterator. 
 * Make (an iterable of) _bigrams_ of items in iterable: `zip(mylist, mylist[1:])`
    ```python
    words = 'A girl has no name'.split()
    bigrams = list(zip(words, words[1:]))
    # bigrams is [('A', 'girl'), ('girl', 'has'), ('has', 'no'), ('no', 'name')]
    ```
 * _Transpose/Unzip_ an iterable of tuples: `zip(*data)`\
    <sub>Prerequisite: [`*args` and `**kwargs`](https://github.com/kamalbanga/Python-3/wiki/1.-Reference#-and-)</sub>
    ```python
    data = [(1, 2, 3), (4, 5, 6)]
    transposed = list(zip(*data))
    # transposed is [(1, 4), (2, 5), (3, 6)]
    ```
    `zip(*)` is equivalent to unzip/transpose
    ```python
    ls1 = [1, 2, 3, 4, 5]
    ls2 = list('abcde')
    c = list(zip(ls1, ls2)) 
    # c is [(1, 'a'), (2, 'b'), (3, 'c'), (4, 'd'), (5, 'e')]
    d = list(zip(*c)) # equivalent to unzip
    # d is [(1, 2, 3, 4, 5), ('a', 'b', 'c', 'd', 'e')]
    ```
___
_Summary_: The iterators `enumerate`, `zip`, `reversed` are syntax goodies (syntactic sugar) that cover many usual cases to make code more readable and pretty.
___

### Dict's default value: `get`

#### 1. Default value for item not in dictionary
```python
color_weights = {'blue': 1, 'green': 2, 'red': 3}
yellow_weight = color_value['yellow'] if 'yellow' in color_weights else -1
```
**Better**: use `get`
```python
yellow_value = color_value.get('yellow', -1)
```

#### 2. Counting with dictionaries
```python
colors = ['red', 'green', 'red', 'blue', 'green', 'red']

d = {}
for color in colors:
    if color not in d:
        d[color] = 0
    d[color] += 1

# {'blue': 1, 'green': 2, 'red': 3}
```
**Better**
```python
d = {}
for color in colors:
    d[color] = d.get(color, 0) + 1
```
**Use collections** 💪
```python
from collections import Counter

Counter(colors)
```
___

### Grouping
Use **`defaultdict`**
___

### `any` function
Let's simulate an experiment to shuffle 'n' cards each with a unique label in 0...n-1, and then check if any _k_<sup>th</sup> card's label is _k_. 

We will use `sample` function from `random` module for that. `sample` is used for sampling with replacement; `sample(range(n), n)` is equivalent to shuffling the list 0...n-1.
```python
from random import sample
idx_labels = enumerate(sample(range(n), n))
```
To proceed with the experiment:
```python
for idx, label in idx_labels:
  if idx == label:
    print(True)
print(False)
```
**Better**: use **`any`**
```python
outcome = any(idx == label for idx, label in idx_labels)
print(outcome)
```
We could also have used a list instead of a generator: `any([idx == label for idx, label in idx_labels])`, but obviously generator-expression used above is memory-efficient.
___

### The `with` statement
```python
foo = open('/tmp/foo', 'w')
try:
  foo.write('sometext')
finally:
  foo.close()
```
👆code is equivalent to 👇. **Use `with`** 
```python
with open('/tmp/foo', 'w') as handle:
  handle.write('sometext')
```
___

### Comprehensions
```python
squares = list(map(lambda x: x**2, range(1,10)))
even_squares = list(map(lambda x: x**2, filter(lambda x: x % 2 == 0, range(1,10))))
```
List comprehensions 👇are more readable and _pythonic_! 🤘
```python
squares = [x**2 for x in range(1,10)]
even_squares = [x**2 for x in range(1,10) if x % 2 == 0]
```
___

### Specialized tool beats a general purpose tool
Specialized tools usually outperform or are more accurate than general purpose tools
 - `math.sqrt(x)` is more accurate than `x ** 0.5`
 - `math.log2()` is exact for powers of two
    ```python
    from math import log, log2
    all(log(2 ** x, 2) == x for x in range(100)) # False
    all(log2(2 ** x) == x for x in range(100)) # True
    ```
 - In PySpark, `key_value_rdd.countByKey()` is way faster than`key_value_rdd.groupBy().mapValues(len).collect()` because of less shuffling involved.
