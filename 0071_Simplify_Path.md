# Problem
Link: https://leetcode.com/problems/simplify-path/description/
```
Given an absolute path for a Unix-style file system, which begins with a slash '/', transform this path into its simplified canonical path.

In Unix-style file system context, a single period '.' signifies the current directory, a double period ".." denotes moving up one directory level, and multiple slashes such as "//" are interpreted as a single slash. In this problem, treat sequences of periods not covered by the previous rules (like "...") as valid names for files or directories.

The simplified canonical path should adhere to the following rules:

It must start with a single slash '/'.
Directories within the path should be separated by only one slash '/'.
It should not end with a slash '/', unless it's the root directory.
It should exclude any single or double periods used to denote current or parent directories.
Return the new path.

Example 1:

Input: path = "/home/"

Output: "/home"

Explanation:

The trailing slash should be removed.

 
Example 2:

Input: path = "/home//foo/"

Output: "/home/foo"

Explanation:

Multiple consecutive slashes are replaced by a single one.

Example 3:

Input: path = "/home/user/Documents/../Pictures"

Output: "/home/user/Pictures"

Explanation:

A double period ".." refers to the directory up a level.

Example 4:

Input: path = "/../"

Output: "/"

Explanation:

Going one level up from the root directory is not possible.

Example 5:

Input: path = "/.../a/../b/c/../d/./"

Output: "/.../b/d"

Explanation:

"..." is a valid name for a directory in this problem.

```

## Approach
- Split the string by '/'
- Iteration
  - '.' - do nothing
  - '..' - pop from stack if stack is not empty
  - else - push into stack
- build the full path from the stack


## Code
### Solution with Strnig split
```
class Solution {
    public String simplifyPath(String path) {
        if(path == null || path.isEmpty())
            return "/";

        String[] paths = path.split("/");
        Deque<String> queue = new ArrayDeque<>();
        StringBuilder result = new StringBuilder();

        for(String p : paths) {
            if(p == null || p.isEmpty() || p.equals(".")) {
                continue;
            }
            else if(p.equals("..")) {
                if(!queue.isEmpty()) 
                    queue.removeLast();
            }
            else {
                queue.add(p);
            }
        }

        if(queue.isEmpty())
            result.append("/");
        else {
            while(!queue.isEmpty()) {
                result.append("/").append(queue.remove());
            }
        }
        return result.toString();
    }
}
```
### Solution without string split
```
class Solution {
    public String simplifyPath(String path) {
        if(path == null || path.length() == 0)
            return "";
        
        char arr[] = path.toCharArray();
        Deque<String> stack = new ArrayDeque<>();

        StringBuilder sbr = new StringBuilder();
        for(char c : arr) {
            if(c == '/') {
                String dir = sbr.toString();
                if(dir.equals("..")) {
                    if(!stack.isEmpty())
                        stack.removeFirst();
                }
                else if(!dir.isEmpty() && !dir.equals(".")) {
                    stack.offerFirst(dir);
                }
                sbr.setLength(0);
            }
            else {
                sbr.append(c);
            }
        }

        if(!sbr.isEmpty()) {
            String dir = sbr.toString();
            if(dir.equals("..")) {
                if(!stack.isEmpty())
                    stack.removeFirst();
            }
            else if(!dir.isEmpty() && !dir.equals(".")) {
                stack.offerFirst(dir);
            }
        }

        sbr.setLength(0);
        while(!stack.isEmpty()) {
            sbr.append('/').append(stack.removeLast());
        }

        return sbr.isEmpty()? "/" : sbr.toString();
    }
}
```
