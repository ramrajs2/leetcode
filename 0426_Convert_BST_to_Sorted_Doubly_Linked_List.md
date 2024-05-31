# 426. Convert Binary Search Tree to Sorted Doubly Linked List
## Problem
Link: https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/description/
```
Convert a Binary Search Tree to a sorted Circular Doubly-Linked List in place.

You can think of the left and right pointers as synonymous to the predecessor and successor pointers in a doubly-linked list. For a circular doubly linked list, the predecessor of the first element is the last element, and the successor of the last element is the first element.

We want to do the transformation in place. After the transformation, the left pointer of the tree node should point to its predecessor, and the right pointer should point to its successor. You should return the pointer to the smallest element of the linked list.
```

## Approach
Recursive solution
- We need both first and last nodes in a converted list
- Convert left sub-tree and get first and last node from that
- Convert right sub-tree and get first and last node from that
- Update the pointers
- In the end, connect the first and last to complete the circular list
- return the head 

## Code
```
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/

class NodePair {
    Node first;
    Node last;
}

class Solution {
    public Node treeToDoublyList(Node root) {
        if(root == null)
            return null;
        NodePair p = convertToDoublyList(root);

        // complete the circular connection
        rewire(p.last, p.first);

        return p.first;
    }

    private NodePair convertToDoublyList(Node root) {
        if(root == null)
            return new NodePair();

        // initialize the result with root
        NodePair result = new NodePair();
        result.first = root;
        result.last = root;
        
        if(root.left != null) {
            NodePair left = convertToDoublyList(root.left);
            rewire(left.last, root);
            result.first = left.first;
        }

        if(root.right != null) {
            NodePair right = convertToDoublyList(root.right);
            rewire(root, right.first);
            result.last = right.last;
        }
        return result;
    }

    private void rewire(Node n1, Node n2) {
        n1.right = n2;
        n2.left = n1;
    }
}
```
