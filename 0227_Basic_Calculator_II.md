# Question
https://leetcode.com/problems/basic-calculator-ii/
```
Given a string s which represents an expression, evaluate this expression and return its value. 

The integer division should truncate toward zero.

You may assume that the given expression is always valid. All intermediate results will be in the range of [-231, 231 - 1].

Note: You are not allowed to use any built-in function which evaluates strings as mathematical expressions, such as eval().

 

Example 1:

Input: s = "3+2*2"
Output: 7
Example 2:

Input: s = " 3/2 "
Output: 1
Example 3:

Input: s = " 3+5 / 2 "
Output: 5

```

## Solution with Stack
### Intuition:
- Process one pair of numbers at a time. 
- Assume first number is already processed and stored in stack. Just process second number based on the last operator
- A + B => Assuming A is already processed, if lastOp is '+' => push B into stack
- A - B => Assuming A is already processed, if lastOp is '-' => push -B into stack 
- A * B => Assuming A is already processed, if lastOp is '*' => pop, multiply with B, push result into stack
- A / B => Assuming A is already processed, if lastOp is '/' => pop, divide by B, push result into stack

### Why do I assume A is processed already
- For a given expression, we process from left to right
- Instead of consfusing around the edge cases, we can assume string with additional prefix and suffix. For example
  - A * B + C => `0 ? A * B + C ?`
  - val is init to 0
  - lastOp is init to ?
  - end of string is marked by ? => this helps in processing the final value in the expression
- As I move along, always A is a processed value which is stored in the stack

### Code
```
class Solution {
    public int calculate(String s) {
        Stack<Integer> stack = new Stack<>();
        int val = 0;
        char lastOp = '?';
        int n = s.length();
        char c;

        for(int i=0; i<=n; i++) {
            if(i != n) c = s.charAt(i); 
            else c = '?'; 
            // ? marks the end of string

            if(Character.isDigit(c)) {
                val = val*10 + c - '0';
            }
            else if(c == ' ') {
                continue;
            }
            else {
                switch(lastOp) {
                    case ' ' : 
                    case '+' : stack.push(val); break;
                    case '-' : stack.push(-val); break; 
                    case '*' : stack.push(stack.pop() * val); break;
                    case '/' : stack.push(stack.pop() / val); break;
                }
                val = 0;
                lastOp = c;
            }
        }

        int sum = 0;
        while(!stack.isEmpty()) {
            sum += stack.pop();
        }
        return sum;
    }
}
```
## Solution without Stack
### Intuition
Something similar to Sliding window technique
Consider the expression and pointers below:
```
         A + B * C - D * E
  c1 lp c2 c
```
Where
- c1 = operand1
- c2 = operand2
- lp = last operation
- c = current operation

These four pointers slides together.
- First, iteratively c2 is calculated
- when a new operator(c) is intercepted, depending on lp, we update the variables and move the window forward:
```
# iteration 1
        A  +  B * C - D * E
        ^  ^  ^ ^
       c1 lp c2 c

# iteration 2
         A +  B  *  C  - D * E
              ^  ^  ^  ^
             c1 lp c2  c
. . .
 . . .
```

### Also Consider
- if multiple * operations are following one another:
Ex: A + B * C * D + E
  - keep updating the curr1 until +/- is intercepted

- if only one number is the input
Ex: 22
 - The curr number1 should to be added after the loop

- if only multiplication is in the expression
Ex: 22 * 12 * 10
 - The curr number1 should to be added after the loop
### Code
```
class Solution {
    public int calculate(String s) {
        int curr1 = 0;
        int curr2 = 0;
        char lastOp = '?';
        int n = s.length();
        char c;
        int sum = 0; // 7

        for(int i=0; i<=n; i++) {
            if(i != n) c = s.charAt(i); 
            else c = '?'; 
            // ? marks the end of string

            if(Character.isDigit(c)) {
                curr2 = curr2*10 + c - '0'; // 2
            }
            else if(c == ' ') {
                continue;
            }
            else {
                // System.out.println(curr1 + " " + lastOp + " " + curr2);
                switch(lastOp) {
                    case '?' : 
                    case '+' : sum += curr1; curr1 = curr2; break; 
                    case '-' : sum += curr1; curr1 = -curr2; break; 
                    case '*' : curr1 = curr1*curr2; break;
                    case '/' : curr1 = curr1/curr2; break;
                }
                // System.out.println(">>> " + sum);
                curr2 = 0;
                lastOp = c; // +
            }
        }

        // if lastOp was "+ or - " in the expression, only curr1 is added to sum but curr2 is missed. curr1 is set to curr2 and curr2 is set to 0.
        // if lastOp was "* or /" in the expression, curr1=curr1*curr2 is calculated but not added to sum. but it is set into curr1 
        sum += curr1;

        return sum;
    }
}
```
### Approach
- When ?/+/- is intercepted, add c1 to sum
  - A + B * C => Sum = A + (B * C)
  - Iteartions
    - c1 = A; Sum += c1
    - c1 = B; c2 = C
    - c1 = B*C; c2 = 0;
    - outside loop: sum += c1
- When * or / is intercepted, calculate and set it to c1
  - A * B * C + D + E / F => Sum = (A * B * C) + D + (E / F)
  - Iteartions
    - '*': c1 = A; C2 = B; => c1 = A*B; c2 = 0;
    - '*': c1 = AB; c2 = C; => c1 = ABC; c2 = 0;
    - '+': c1 = ABC; c2 = D; => sum += c1; c1 = D; c2 = 0;
    - '+': c1 = D; c2 = E; => sum += c1; c1 = E; c2 = 0;
    - '?': outside loop: sum += c1;
```
