# 708. Insert into a Sorted Circular Linked List

## Problem
Link: https://leetcode.com/problems/insert-into-a-sorted-circular-linked-list/description/
```
Given a Circular Linked List node, which is sorted in non-descending order, write a function to insert a value insertVal into the list such that it remains a sorted circular list. The given node can be a reference to any single node in the list and may not necessarily be the smallest value in the circular list.

If there are multiple suitable places for insertion, you may choose any place to insert the new value. After the insertion, the circular list should remain sorted.

If the list is empty (i.e., the given node is null), you should create a new single circular list and return the reference to that single node. Otherwise, you should return the originally given node.

Example 1: 
Input: head = [3,4,1], insertVal = 2
Output: [3,4,1,2]
Explanation: In the figure above, there is a sorted circular list of three elements. You are given a reference to the node with value 3, and we need to insert 2 into the list. The new node should be inserted between node 1 and node 3. After the insertion, the list should look like this, and we should still return node 3.

Example 2:
Input: head = [], insertVal = 1
Output: [1]
Explanation: The list is empty (given head is null). We create a new single circular list and return the reference to that single node.
Example 3:

Input: head = [1], insertVal = 0
Output: [1,0]
```

## Intuition
Given a number, insert into sorted linked list
- Iterate through the list once
- Find the location(prev & curr nodes) to insert
- insert between the nodes: prev and curr
- if location is not found, insert into end

### Cases to consider:
Assume the list below:
8 - 1 - 4 - 6

1. duplicate insert: insert 1 or insert 4
  - whenever the curr pointer is same as insert val, we can insert there
2. proper insert: insert 5
  - whenever we find two nodes where the insert node can fit into
3. circular edge insert: insert 9 or insert 0
  - two nodes where the head and tail meets
4. array edge insert: insert 7
  - ends of the actual array given
  - If the iteration is complete and we didn't find the fitting location, then this is the place that is unchecked so far. Insert here


## Code
```
/*
// Definition for a Node.
class Node {
    public int val;
    public Node next;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, Node _next) {
        val = _val;
        next = _next;
    }
};
*/

class Solution {
    public Node insert(Node head, int insertVal) {
        Node insert = new Node(insertVal);
        if(head == null) {
            insert.next = insert;
            return insert;
        }

        Node prev = head;
        Node curr = head.next;
        boolean inserted = false;

        while(curr != head) {
            if(fits(prev, curr, insert)) { // 1,3,0
                insert(prev, curr, insert);
                inserted = true;
                break;
            }
            prev = curr; 
            curr = curr.next;
        }

        // array edge insert
        if(!inserted) {
            insert(prev, curr, insert);
        }

        return head;
    }

    private boolean fits(Node prev, Node curr, Node insert) {
        // duplicate case
        // p, c
        // 1, 3, insert 3
        if(curr.val == insert.val) {
            return true;
        }
        // proper case
        // 1, 3, insert 2
        if(prev.val < insert.val && insert.val < curr.val) {
            return true;
        }

        // circular edge insert
        //3, 1, insert 0
        //3, 1, insert 4
        if(prev.val > curr.val && 
            (insert.val < curr.val || prev.val < insert.val)) {
            return true;
        }

        return false;
    }

    private void insert(Node prev, Node curr, Node insert) {
        prev.next = insert;
        insert.next = curr;
    }
}

/**

6 - 8 - 1 - 4 - 6


1 - 4 - 6 - 8 - 1
            p   c
insert
0 - p > c && num < c --> insert (p, num, c)
5 - p < num < c --> insert (p, num, c)
9 - p > c && num > p --> insert (p, num, c)

1 - num == c --> insert (p, num, c)
8 - num == c --> insert (p, num, c)
 */
```
