---
layout: page
---

# Problem 3: Kishan-Tier

BiNode:  Consider  a  simple  data  structure  called  BiNode,  which  has  pointers  to  two  other  nodes,
```java
public  class  BiNode  {
    public  BiNode  node1,  node2;
    public  int  data;
}
```

The  data  structure  BiNode  could  be  used  to  represent  both  a  binary  tree  (where  node1  is  the  left node  and  node2  is  the  right  node)  or  a  doubly  linked  list  (where  node1 is  the  previous  node  and node2  is  the  next  node).  Implement  a  method  to  convert  a  binary  search  tree  (implemented  with BiNode)  into  a  doubly  linked  list.  The  values  should  be  kept  in  order  and  the  operation  should  be performed  in  place  (that  is,  on  the  original  data  structure).

### Python solution by @Yao

```python
class BiNode:
    def __init__(self, left, right, data):
        self.left = left
        self.right = right
        self.data = data

    def __str__(self):
        return f"node: left={self.left} self={self.data} right={self.right}"

    def print_linked_list(self):
        cur_node = self
        while cur_node.left:
            cur_node = cur_node.left

        while True:
            print(cur_node.data, " ", end="")
            if cur_node.right:
                cur_node = cur_node.right
            else:
                break


def bst_2_linked_list(cur_node: BiNode, left_link: BiNode, right_link: BiNode):
    if cur_node.left:
        bst_2_linked_list(cur_node.left, left_link, cur_node)
    elif left_link:
        cur_node.left = left_link
        left_link.right = cur_node

    if cur_node.right:
        bst_2_linked_list(cur_node.right, cur_node, right_link)
    elif right_link:
        cur_node.right = right_link
        right_link.left = cur_node


if __name__ == "__main__":
    n4 = BiNode(None, None, 4)
    n0 = BiNode(None, n4, 0)
    n13 = BiNode(None, None, 13)
    n14 = BiNode(n13, None, 14)
    n12 = BiNode(None, n14, 12)
    n16 = BiNode(n12, None, 16)
    n8 = BiNode(n0, n16, 8)
    n28 = BiNode(None, None, 28)
    n32 = BiNode(n28, None, 32)
    n52 = BiNode(None, None, 52)
    n48 = BiNode(None, n52, 48)
    n40 = BiNode(n32, n48, 40)
    n24 = BiNode(n8, n40, 24)  # root

    bst_2_linked_list(n24, None, None)
    n24.print_linked_list()
```

### Python solution by @FractalBach

```python
firstNode = None
prev = None


def inOrder(node):
    if node is None:
        return
    inOrder(node.node1)
    visit(node)
    inOrder(node.node2)


def visit(node):
    global prev, firstNode
    if prev is None:
        firstNode = node
    else:
        prev.node2 = node
        node.node1 = prev
    prev = node


def sweepRight(node):
    a = None
    b = None
    if node is None:
        return
    if node.node1 is not None:
        a = node.node1.data
    if node.node2 is not None:
        b = node.node2.data
    print(f"Node: {node.data},  Prev: {a},  Next: {b}")
    sweepRight(node.node2)


if __name__ == "__main__":
    inOrder(tree)
    sweepRight(firstNode)
```
