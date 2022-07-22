
# The structure of this doc

- only first level markdown headings (#)
- setup code to play with
- a bunch of questions roughly in order of: LL, trees, graphs, two pointers, misc
- Solutions in multiple languages are beneath each Q

# LL setup Python

```python
import random

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def build(head):
    for i in range(7):
        setattr(head, 'next', ListNode(random.randrange(100)))
        head = head.next

l1 = ListNode(69); build(l1)
```

# LL setup C

```c
struct Node {
    int data;
    Node *next;
};
```

# Tree setup Python

```python
class TreeNode:
    def __init__(self, x, left=None, right=None):
        self.val = x
        self.left = None
        self.right = None

def init_mytree():
    t = TreeNode('F')
    t.left = TreeNode('B')
    t.right = TreeNode('G')
    t.left.left = TreeNode('A')
    t.left.right = TreeNode('D')
    t.left.right.left = TreeNode('C')
    t.left.right.right = TreeNode('E')
    t.right.right = TreeNode('I')
    t.right.right.left = TreeNode('H')

    return t

prac_tree = init_mytree()
```

# Graph setup Python

```python
tripathi = {'A': ['B', 'D', 'G'],
            'B': ['A', 'E', 'F'],
            'C': ['F', 'H'],
            'D': ['A', 'F'],
            'E': ['B', 'G'],
            'F': ['B', 'C', 'D'],
            'G': ['A', 'E'],
            'H': ['C']}
# ['A', 'B', 'E', 'G', 'F', 'C', 'H', 'D']

graph_programiz = {'0': set(['1', '2']),
         '1': set(['0', '3', '4']),
         '2': set(['0']),
         '3': set(['1']),
         '4': set(['2', '3'])}

graph_educativeio = {
    'A' : ['B','C'],
    'B' : ['D', 'E'],
    'C' : ['F'],
    'D' : [],
    'E' : ['F'],
    'F' : []
}
```

# LL: `printLL(head)`

```python
def printLL(head):
    print(head.val, end=' ')
    if head.next:
        printLL(head.next)
```

```python
def printLL(head):
    if head is None:
        print('X')
    else:
        print(head.val, end=' -> ')
        printLL(head.next)
```

```python
def printLL_it(head):
    while head:
        print(head.val, end=' ')
        head = head.next
```


# LL: reverse a linked list, `reverseLL`


# Tree: `inOrder(root)`

```python
def inOrder(root):
    if root:
        inOrder(root.left)
        print(root.val, end=' ')
        inOrder(root.right)
```

# Graph: `dfs(...)`

```python
def dfs(graph, node, visited=None):
    if visited is None:
        visited = []
    if node not in visited:
        visited.append(node)
        for adjacent in sorted(graph[node]):
            dfs(graph, adjacent, visited)
    return visited
```