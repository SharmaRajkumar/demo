# Dynamic Programming: 0/1 Knapsack Pattern (Java Implementation)

## Table of Contents
- [Introduction to Dynamic Programming](#introduction-to-dynamic-programming)
- [Pattern 1: 0/1 Knapsack](#pattern-1-01-knapsack)
  - [0/1 Knapsack Problem](#01-knapsack-problem)
  - [Equal Subset Sum Partition](#equal-subset-sum-partition)
  - [Subset Sum](#subset-sum)
  - [Minimum Subset Sum Difference](#minimum-subset-sum-difference)
  - [Count of Subset Sum](#count-of-subset-sum)
  - [Target Sum](#target-sum)

---

## Introduction to Dynamic Programming

**Dynamic Programming (DP)** is an algorithmic technique for solving optimization problems by breaking them down into simpler subproblems and utilizing the fact that the optimal solution to the overall problem depends upon the optimal solution to its subproblems.

### Example: Fibonacci Numbers

Fibonacci numbers are a series where each number is the sum of the two preceding numbers: `0, 1, 1, 2, 3, 5, 8...`

The recursive formula is:
```
Fib(n) = Fib(n-1) + Fib(n-2), for n > 1
```

This shows we can use DP to solve this problem by breaking it into smaller subproblems.

---

## Characteristics of Dynamic Programming

### 1. Overlapping Subproblems

A problem has overlapping subproblems if finding its solution involves solving the same subproblem multiple times.

**Example:** Computing `fib(4)`:
```
                    fib(4)
                   /      \
              fib(3)      fib(2)
              /    \       /    \
         fib(2)  fib(1) fib(1) fib(0)
         /    \
    fib(1)  fib(0)
```

Notice `fib(2)` is evaluated twice and `fib(1)` three times.

### 2. Optimal Substructure Property

A problem has optimal substructure if its overall optimal solution can be constructed from the optimal solutions of its subproblems.

For Fibonacci: `Fib(n) = Fib(n-1) + Fib(n-2)` clearly shows this property.

---

## Dynamic Programming Methods

### 1. Top-down with Memoization

Start with the bigger problem and recursively solve smaller subproblems. Cache results to avoid recomputation.

**Fibonacci with Memoization:**
```java
public class FibonacciMemoization {
    public static int calculateFibonacci(int n) {
        Integer[] memoize = new Integer[n + 1];
        return fib(memoize, n);
    }
    
    private static int fib(Integer[] memoize, int n) {
        if (n < 2) return n;
        
        // Return cached result if available
        if (memoize[n] != null) return memoize[n];
        
        memoize[n] = fib(memoize, n - 1) + fib(memoize, n - 2);
        return memoize[n];
    }
    
    public static void main(String[] args) {
        System.out.println("5th Fibonacci is: " + calculateFibonacci(5));
        System.out.println("6th Fibonacci is: " + calculateFibonacci(6));
        System.out.println("7th Fibonacci is: " + calculateFibonacci(7));
    }
}
```

### 2. Bottom-up with Tabulation

Solve the problem bottom-up by solving all related subproblems first, typically using an n-dimensional table.

**Fibonacci with Tabulation:**
```java
public class FibonacciTabulation {
    public static int calculateFibonacci(int n) {
        if (n < 2) return n;
        
        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 1;
        
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
    
    public static void main(String[] args) {
        System.out.println("5th Fibonacci is: " + calculateFibonacci(5));
        System.out.println("6th Fibonacci is: " + calculateFibonacci(6));
        System.out.println("7th Fibonacci is: " + calculateFibonacci(7));
    }
}
```

**Note:** We always start with a brute-force recursive solution, then apply memoization and tabulation techniques.

---

# Pattern 1: 0/1 Knapsack

The 0/1 Knapsack pattern is based on the famous problem where we efficiently use Dynamic Programming. We'll develop understanding by starting with brute-force recursive solutions to see overlapping subproblems, then apply memoization and bottom-up DP.

---

## 0/1 Knapsack Problem

**Problem:** Given weights and profits of N items, put these items in a knapsack with capacity C to get maximum profit. Each item can only be selected once.

**Example:**
```
Items:    { Apple, Orange, Banana, Melon }
Weights:  { 2,     3,      1,      4     }
Profits:  { 4,     5,      3,      7     }
Knapsack capacity: 5

Possible combinations:
- Apple + Orange (weight 5) => 9 profit
- Apple + Banana (weight 3) => 7 profit
- Orange + Banana (weight 4) => 8 profit
- Banana + Melon (weight 5) => 10 profit ✓ BEST

Answer: Banana + Melon gives maximum profit of 10
```

### Brute Force Solution

For each item, we have two options:
1. **Include** the item if it doesn't exceed capacity
2. **Exclude** the item

```java
public class KnapsackBruteForce {
    
    public static int solveKnapsack(int[] profits, int[] weights, int capacity) {
        return knapsackRecursive(profits, weights, capacity, 0);
    }
    
    private static int knapsackRecursive(int[] profits, int[] weights, 
                                         int capacity, int currentIndex) {
        // Base case: no capacity left or no items left
        if (capacity <= 0 || currentIndex >= profits.length) {
            return 0;
        }
        
        // Recursive call after choosing the item at currentIndex
        // Only if the weight doesn't exceed capacity
        int profit1 = 0;
        if (weights[currentIndex] <= capacity) {
            profit1 = profits[currentIndex] + 
                     knapsackRecursive(profits, weights, 
                                      capacity - weights[currentIndex], 
                                      currentIndex + 1);
        }
        
        // Recursive call after excluding the item at currentIndex
        int profit2 = knapsackRecursive(profits, weights, capacity, currentIndex + 1);
        
        // Return maximum of the two options
        return Math.max(profit1, profit2);
    }
    
    public static void main(String[] args) {
        int[] profits = {1, 6, 10, 16};
        int[] weights = {1, 2, 3, 5};
        int capacity = 7;
        
        int maxProfit = solveKnapsack(profits, weights, capacity);
        System.out.println("Total knapsack profit: " + maxProfit);
        // Output: 22
        
        capacity = 6;
        maxProfit = solveKnapsack(profits, weights, capacity);
        System.out.println("Total knapsack profit: " + maxProfit);
        // Output: 17
    }
}
```

**Time Complexity:** O(2^n) - We have two recursive calls for each item  
**Space Complexity:** O(n) - Recursion stack depth

**Explanation:**
- For each item, we explore two possibilities: include it or exclude it
- If we include an item, we add its profit and reduce the remaining capacity
- We take the maximum profit from both choices
- The recursion tree has 2^n nodes (exponential)

---

### Overlapping Subproblems

Let's visualize the recursive calls with `capacity=7, currentIndex=i`:

```
                     c:7, i:0
                    /        \
              c:6, i:1      c:7, i:1
              /      \       /      \
         c:4,i:2  c:6,i:2  c:4,i:2  c:7,i:2
                              ↑
                      Same subproblem!
```

We clearly see `c:4, i:2` being called multiple times - this is the **overlapping subproblems** pattern.

---

### Top-down DP with Memoization

We can store results of solved subproblems in a 2D array since we have two changing values: `capacity` and `currentIndex`.

```java
public class KnapsackMemoization {
    
    public static int solveKnapsack(int[] profits, int[] weights, int capacity) {
        Integer[][] memo = new Integer[profits.length][capacity + 1];
        return knapsackRecursive(memo, profits, weights, capacity, 0);
    }
    
    private static int knapsackRecursive(Integer[][] memo, int[] profits, 
                                         int[] weights, int capacity, 
                                         int currentIndex) {
        // Base case
        if (capacity <= 0 || currentIndex >= profits.length) {
            return 0;
        }
        
        // Check if we have already solved this subproblem
        if (memo[currentIndex][capacity] != null) {
            return memo[currentIndex][capacity];
        }
        
        // Recursive call after choosing the item at currentIndex
        int profit1 = 0;
        if (weights[currentIndex] <= capacity) {
            profit1 = profits[currentIndex] + 
                     knapsackRecursive(memo, profits, weights, 
                                      capacity - weights[currentIndex], 
                                      currentIndex + 1);
        }
        
        // Recursive call after excluding the item at currentIndex
        int profit2 = knapsackRecursive(memo, profits, weights, 
                                       capacity, currentIndex + 1);
        
        // Store the result in memo table
        memo[currentIndex][capacity] = Math.max(profit1, profit2);
        return memo[currentIndex][capacity];
    }
    
    public static void main(String[] args) {
        int[] profits = {1, 6, 10, 16};
        int[] weights = {1, 2, 3, 5};
        
        int maxProfit = solveKnapsack(profits, weights, 7);
        System.out.println("Total knapsack profit: " + maxProfit);
        // Output: 22
        
        maxProfit = solveKnapsack(profits, weights, 6);
        System.out.println("Total knapsack profit: " + maxProfit);
        // Output: 17
    }
}
```

**Time Complexity:** O(N × C) - We solve each subproblem only once  
**Space Complexity:** O(N × C) + O(N) = O(N × C) - Memo array + recursion stack

**Explanation:**
- We use a 2D Integer array (using Integer instead of int to check for null)
- Before computing, we check if the result is already in our memo table
- This eliminates redundant calculations significantly
- Each subproblem is solved exactly once

---

### Bottom-up Dynamic Programming

We build our solution from the ground up. `dp[i][c]` represents the maximum profit for capacity `c` using the first `i` items.

For each item at index `i` and capacity `c`, we have two options:
1. **Exclude** the item: `dp[i-1][c]`
2. **Include** the item (if weight allows): `profit[i] + dp[i-1][c - weight[i]]`

**Formula:**
```
dp[i][c] = max(dp[i-1][c], profit[i] + dp[i-1][c - weight[i]])
```

```java
public class KnapsackBottomUp {
    
    public static int solveKnapsack(int[] profits, int[] weights, int capacity) {
        // Basic validation
        if (capacity <= 0 || profits.length == 0 || 
            weights.length != profits.length) {
            return 0;
        }
        
        int n = profits.length;
        int[][] dp = new int[n][capacity + 1];
        
        // Populate capacity=0 columns (0 profit with 0 capacity)
        for (int i = 0; i < n; i++) {
            dp[i][0] = 0;
        }
        
        // Process first row: with only one item
        for (int c = 0; c <= capacity; c++) {
            if (weights[0] <= c) {
                dp[0][c] = profits[0];
            }
        }
        
        // Process all sub-arrays for all capacities
        for (int i = 1; i < n; i++) {
            for (int c = 1; c <= capacity; c++) {
                int profit1 = 0; // profit including item i
                int profit2 = 0; // profit excluding item i
                
                // Include the item if it doesn't exceed capacity
                if (weights[i] <= c) {
                    profit1 = profits[i] + dp[i - 1][c - weights[i]];
                }
                
                // Exclude the item
                profit2 = dp[i - 1][c];
                
                // Take maximum
                dp[i][c] = Math.max(profit1, profit2);
            }
        }
        
        // Maximum profit will be at the bottom-right corner
        return dp[n - 1][capacity];
    }
    
    public static void main(String[] args) {
        int[] profits = {1, 6, 10, 16};
        int[] weights = {1, 2, 3, 5};
        
        int maxProfit = solveKnapsack(profits, weights, 7);
        System.out.println("Total knapsack profit: " + maxProfit);
        // Output: 22
        
        maxProfit = solveKnapsack(profits, weights, 6);
        System.out.println("Total knapsack profit: " + maxProfit);
        // Output: 17
    }
}
```

**Time Complexity:** O(N × C)  
**Space Complexity:** O(N × C)

**Explanation of the DP table:**
```
Items: {1,6,10,16}, Weights: {1,2,3,5}, Capacity: 7

     c: 0  1  2  3  4  5  6  7
i=0:    0  1  1  1  1  1  1  1
i=1:    0  1  6  7  7  7  7  7
i=2:    0  1  6 10 11 16 17 17
i=3:    0  1  6 10 11 16 17 22

Bottom-right cell (22) is our answer!
```

---

### Finding Selected Items

To find which items were selected, we backtrack through the DP table:

```java
public class KnapsackWithItems {
    
    public static int solveKnapsack(int[] profits, int[] weights, int capacity) {
        if (capacity <= 0 || profits.length == 0 || 
            weights.length != profits.length) {
            return 0;
        }
        
        int n = profits.length;
        int[][] dp = new int[n][capacity + 1];
        
        // Populate the DP table (same as before)
        for (int i = 0; i < n; i++) {
            dp[i][0] = 0;
        }
        
        for (int c = 0; c <= capacity; c++) {
            if (weights[0] <= c) {
                dp[0][c] = profits[0];
            }
        }
        
        for (int i = 1; i < n; i++) {
            for (int c = 1; c <= capacity; c++) {
                int profit1 = 0;
                int profit2 = 0;
                
                if (weights[i] <= c) {
                    profit1 = profits[i] + dp[i - 1][c - weights[i]];
                }
                
                profit2 = dp[i - 1][c];
                dp[i][c] = Math.max(profit1, profit2);
            }
        }
        
        // Print selected items
        printSelectedItems(dp, weights, profits, capacity);
        
        return dp[n - 1][capacity];
    }
    
    private static void printSelectedItems(int[][] dp, int[] weights, 
                                           int[] profits, int capacity) {
        System.out.print("Selected items: ");
        int totalProfit = dp[weights.length - 1][capacity];
        int remainingCapacity = capacity;
        
        for (int i = weights.length - 1; i > 0; i--) {
            // If profit didn't come from the row above, item i was selected
            if (totalProfit != dp[i - 1][remainingCapacity]) {
                System.out.print("(" + weights[i] + " lbs, $" + profits[i] + ") ");
                remainingCapacity -= weights[i];
                totalProfit -= profits[i];
            }
        }
        
        // Check if first item was selected
        if (totalProfit != 0) {
            System.out.print("(" + weights[0] + " lbs, $" + profits[0] + ")");
        }
        
        System.out.println();
    }
    
    public static void main(String[] args) {
        int[] profits = {1, 6, 10, 16};
        int[] weights = {1, 2, 3, 5};
        
        int maxProfit = solveKnapsack(profits, weights, 7);
        System.out.println("Total knapsack profit: $" + maxProfit);
        // Output:
        // Selected items: (5 lbs, $16) (2 lbs, $6) 
        // Total knapsack profit: $22
    }
}
```

**Backtracking Logic:**
1. Start from `dp[n-1][capacity]` (bottom-right)
2. If `dp[i][c] ≠ dp[i-1][c]`, item `i` was included
3. Subtract item's weight and profit, move to `dp[i-1][c - weight[i]]`
4. If `dp[i][c] == dp[i-1][c]`, item `i` was not included, move to `dp[i-1][c]`

---

### Space Optimization Challenge

**Can we achieve O(C) space complexity?**

Yes! We only need the previous row to calculate the current row.

```java
public class KnapsackSpaceOptimized {
    
    public static int solveKnapsack(int[] profits, int[] weights, int capacity) {
        if (capacity <= 0 || profits.length == 0 || 
            weights.length != profits.length) {
            return 0;
        }
        
        int n = profits.length;
        // We only need two rows
        int[][] dp = new int[2][capacity + 1];
        
        // Process first item
        for (int c = 0; c <= capacity; c++) {
            if (weights[0] <= c) {
                dp[0][c] = dp[1][c] = profits[0];
            }
        }
        
        // Process remaining items
        for (int i = 1; i < n; i++) {
            for (int c = 1; c <= capacity; c++) {
                int profit1 = 0;
                int profit2 = 0;
                
                if (weights[i] <= c) {
                    profit1 = profits[i] + dp[(i - 1) % 2][c - weights[i]];
                }
                
                profit2 = dp[(i - 1) % 2][c];
                dp[i % 2][c] = Math.max(profit1, profit2);
            }
        }
        
        return dp[(n - 1) % 2][capacity];
    }
    
    public static void main(String[] args) {
        int[] profits = {1, 6, 10, 16};
        int[] weights = {1, 2, 3, 5};
        
        System.out.println("Total knapsack profit: " + 
                          solveKnapsack(profits, weights, 7));
        // Output: 22
    }
}
```

**Time Complexity:** O(N × C)  
**Space Complexity:** O(2 × C) = O(C)

**Further Optimization - Single Array:**

```java
public class KnapsackSingleArray {
    
    public static int solveKnapsack(int[] profits, int[] weights, int capacity) {
        if (capacity <= 0 || profits.length == 0 || 
            weights.length != profits.length) {
            return 0;
        }
        
        int[] dp = new int[capacity + 1];
        
        // Process first item
        for (int c = 0; c <= capacity; c++) {
            if (weights[0] <= c) {
                dp[c] = profits[0];
            }
        }
        
        // Process remaining items (iterate capacity in reverse!)
        for (int i = 1; i < profits.length; i++) {
            for (int c = capacity; c >= 0; c--) {
                int profit1 = 0;
                int profit2 = dp[c]; // excluding item i
                
                if (weights[i] <= c) {
                    profit1 = profits[i] + dp[c - weights[i]];
                }
                
                dp[c] = Math.max(profit1, profit2);
            }
        }
        
        return dp[capacity];
    }
    
    public static void main(String[] args) {
        int[] profits = {1, 6, 10, 16};
        int[] weights = {1, 2, 3, 5};
        
        System.out.println("Total knapsack profit: " + 
                          solveKnapsack(profits, weights, 7));
        // Output: 22
    }
}
```

**Key Insight:** We iterate capacity in **reverse order** to ensure we use values from the previous iteration, not the current one.

**Time Complexity:** O(N × C)  
**Space Complexity:** O(C)

---

## Equal Subset Sum Partition

**Problem:** Given a set of positive numbers, determine if we can partition it into two subsets with equal sum.

**Example:**
```
Input: {1, 2, 3, 4}
Output: true
Explanation: {1, 4} and {2, 3} both sum to 5
```

**Approach:** 
This follows the 0/1 Knapsack pattern. If total sum is `S`, we need to find if there's a subset with sum `S/2`.

### Brute Force Solution

```java
public class EqualSubsetSumBruteForce {
    
    public static boolean canPartition(int[] num) {
        int sum = 0;
        for (int n : num) {
            sum += n;
        }
        
        // If sum is odd, we can't have two equal subsets
        if (sum % 2 != 0) {
            return false;
        }
        
        return canPartitionRecursive(num, sum / 2, 0);
    }
    
    private static boolean canPartitionRecursive(int[] num, int sum, 
                                                 int currentIndex) {
        // Base case: found exact sum
        if (sum == 0) {
            return true;
        }
        
        // Base case: reached end or sum became negative
        if (num.length == 0 || currentIndex >= num.length) {
            return false;
        }
        
        // Recursive call after choosing the number at currentIndex
        if (num[currentIndex] <= sum) {
            if (canPartitionRecursive(num, sum - num[currentIndex], 
                                     currentIndex + 1)) {
                return true;
            }
        }
        
        // Recursive call after excluding the number at currentIndex
        return canPartitionRecursive(num, sum, currentIndex + 1);
    }
    
    public static void main(String[] args) {
        System.out.println("Can partition: " + canPartition(new int[]{1, 2, 3, 4}));
        // Output: true - {1, 4} & {2, 3}
        
        System.out.println("Can partition: " + canPartition(new int[]{1, 1, 3, 4, 7}));
        // Output: true - {1, 3, 4} & {1, 7}
        
        System.out.println("Can partition: " + canPartition(new int[]{2, 3, 4, 6}));
        // Output: false
    }
}
```

**Time Complexity:** O(2^n)  
**Space Complexity:** O(n)

---

### Bottom-up Dynamic Programming

`dp[i][s]` = true if we can make sum `s` from the first `i` numbers.

```java
public class EqualSubsetSumBottomUp {
    
    public static boolean canPartition(int[] num) {
        int n = num.length;
        int sum = 0;
        
        for (int n1 : num) {
            sum += n1;
        }
        
        // If sum is odd, can't partition
        if (sum % 2 != 0) {
            return false;
        }
        
        sum /= 2;
        boolean[][] dp = new boolean[n][sum + 1];
        
        // Populate sum=0 columns (we can always form 0 sum with empty set)
        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }
        
        // With only one number, we can form a subset only when sum equals that number
        for (int s = 1; s <= sum; s++) {
            dp[0][s] = (num[0] == s);
        }
        
        // Process all subsets for all sums
        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= sum; s++) {
                // If we can get sum s without number at index i
                if (dp[i - 1][s]) {
                    dp[i][s] = dp[i - 1][s];
                } else if (s >= num[i]) {
                    // Include number and see if we can find subset for remaining sum
                    dp[i][s] = dp[i - 1][s - num[i]];
                }
            }
        }
        
        // The answer is at the bottom-right corner
        return dp[n - 1][sum];
    }
    
    public static void main(String[] args) {
        System.out.println("Can partition: " + canPartition(new int[]{1, 2, 3, 4}));
        // Output: true
        
        System.out.println("Can partition: " + canPartition(new int[]{1, 1, 3, 4, 7}));
        // Output: true
        
        System.out.println("Can partition: " + canPartition(new int[]{2, 3, 4, 6}));
        // Output: false
    }
}
```

**Time Complexity:** O(N × S) where S is the total sum  
**Space Complexity:** O(N × S)

**DP Table Visualization:**
```
Input: {1, 2, 3, 4}, Target Sum: 5

     s: 0  1  2  3  4  5
i=0:    T  T  F  F  F  F  (num[0]=1)
i=1:    T  T  T  T  F  F  (num[1]=2)
i=2:    T  T  T  T  T  T  (num[2]=3)
i=3:    T  T  T  T  T  T  (num[3]=4)
                       ↑
                  Answer: True!
```

---

## Subset Sum

**Problem:** Given a set of positive numbers, determine if a subset exists whose sum equals a given number S.

This is very similar to Equal Subset Sum Partition. Let's jump to the bottom-up solution:

```java
public class SubsetSum {
    
    public static boolean canPartition(int[] num, int sum) {
        int n = num.length;
        boolean[][] dp = new boolean[n][sum + 1];
        
        // Populate sum=0 columns
        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }
        
        // With only one number, subset only when sum equals that number
        for (int s = 1; s <= sum; s++) {
            dp[0][s] = (num[0] == s);
        }
        
        // Process all subsets for all sums
        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= sum; s++) {
                // Exclude number at index i
                if (dp[i - 1][s]) {
                    dp[i][s] = dp[i - 1][s];
                } else if (s >= num[i]) {
                    // Include number and check remaining sum
                    dp[i][s] = dp[i - 1][s - num[i]];
                }
            }
        }
        
        return dp[n - 1][sum];
    }
    
    public static void main(String[] args) {
        System.out.println("Can find subset: " + 
                          canPartition(new int[]{1, 2, 3, 4}, 6));
        // Output: true - {1, 2, 3}
        
        System.out.println("Can find subset: " + 
                          canPartition(new int[]{1, 2, 7, 1, 5}, 10));
        // Output: true - {1, 2, 7}
        
        System.out.println("Can find subset: " + 
                          canPartition(new int[]{1, 3, 4, 8}, 6));
        // Output: false
    }
}
```

**Time Complexity:** O(N × S)  
**Space Complexity:** O(N × S)

### Space Optimized O(S) Solution

```java
public class SubsetSumOptimized {
    
    public static boolean canPartition(int[] num, int sum) {
        int n = num.length;
        boolean[] dp = new boolean[sum + 1];
        
        // Can always make sum 0 with empty set
        dp[0] = true;
        
        // With only one number
        for (int s = 1; s <= sum; s++) {
            dp[s] = (num[0] == s);
        }
        
        // Process all numbers (iterate sum in reverse!)
        for (int i = 1; i < n; i++) {
            for (int s = sum; s >= 0; s--) {
                // If dp[s] is already true, no need to change
                if (!dp[s] && s >= num[i]) {
                    dp[s] = dp[s - num[i]];
                }
            }
        }
        
        return dp[sum];
    }
    
    public static void main(String[] args) {
        System.out.println("Can find subset: " + 
                          canPartition(new int[]{1, 2, 3, 4}, 6));
        System.out.println("Can find subset: " + 
                          canPartition(new int[]{1, 2, 7, 1, 5}, 10));
        System.out.println("Can find subset: " + 
                          canPartition(new int[]{1, 3, 4, 8}, 6));
    }
}
```

**Time Complexity:** O(N × S)  
**Space Complexity:** O(S)

---

## Minimum Subset Sum Difference

**Problem:** Partition a set into two subsets such that the difference between their sums is minimized.

**Example:**
```
Input: {1, 2, 3, 9}
Output: 3
Explanation: {1, 2, 3} = 6 and {9} = 9, difference = |6-9| = 3
```

**Approach:** 
- Let S = total sum
- Find subset closest to S/2
- The other subset will be S - (subset sum)
- Minimum difference = |S - 2×(subset sum)|

### Bottom-up DP Solution

```java
public class MinimumSubsetSumDifference {
    
    public static int canPartition(int[] num) {
        int n = num.length;
        int sum = 0;
        
        for (int value : num) {
            sum += value;
        }
        
        int requiredSum = sum / 2;
        boolean[][] dp = new boolean[n][requiredSum + 1];
        
        // Populate sum=0 columns
        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }
        
        // With only one number
        for (int s = 1; s <= requiredSum; s++) {
            dp[0][s] = (num[0] == s);
        }
        
        // Process all subsets for all sums
        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= requiredSum; s++) {
                if (dp[i - 1][s]) {
                    dp[i][s] = dp[i - 1][s];
                } else if (s >= num[i]) {
                    dp[i][s] = dp[i - 1][s - num[i]];
                }
            }
        }
        
        // Find the largest sum we can achieve <= requiredSum
        int sum1 = 0;
        for (int i = requiredSum; i >= 0; i--) {
            if (dp[n - 1][i]) {
                sum1 = i;
                break;
            }
        }
        
        int sum2 = sum - sum1;
        return Math.abs(sum2 - sum1);
    }
    
    public static void main(String[] args) {
        System.out.println("Minimum subset difference: " + 
                          canPartition(new int[]{1, 2, 3, 9}));
        // Output: 3
        
        System.out.println("Minimum subset difference: " + 
                          canPartition(new int[]{1, 2, 7, 1, 5}));
        // Output: 0
        
        System.out.println("Minimum subset difference: " + 
                          canPartition(new int[]{1, 3, 100, 4}));
        // Output: 92
    }
}
```

**Time Complexity:** O(N × S)  
**Space Complexity:** O(N × S)

**Explanation:**
1. Build DP table to find all possible sums up to S/2
2. Find the largest sum ≤ S/2 that we can achieve
3. The other subset = S - sum1
4. Minimum difference = |sum2 - sum1|

---

## Count of Subset Sum

**Problem:** Find the total number of subsets whose sum equals a given number S.

**Example:**
```
Input: {1, 1, 2, 3}, S = 4
Output: 3
Explanation: {1, 1, 2}, {1, 3}, {1, 3}
```

### Bottom-up DP Solution

For each position, we have:
- **Exclude:** `dp[i-1][s]`
- **Include:** `dp[i-1][s - num[i]]`
- **Total:** Sum of both

```java
public class CountOfSubsetSum {
    
    public static int countSubsets(int[] num, int sum) {
        int n = num.length;
        int[][] dp = new int[n][sum + 1];
        
        // Populate sum=0 columns (one way: empty set)
        for (int i = 0; i < n; i++) {
            dp[i][0] = 1;
        }
        
        // With only one number
        for (int s = 1; s <= sum; s++) {
            dp[0][s] = (num[0] == s) ? 1 : 0;
        }
        
        // Process all subsets for all sums
        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= sum; s++) {
                // Exclude the number
                dp[i][s] = dp[i - 1][s];
                
                // Include the number if it doesn't exceed sum
                if (s >= num[i]) {
                    dp[i][s] += dp[i - 1][s - num[i]];
                }
            }
        }
        
        return dp[n - 1][sum];
    }
    
    public static void main(String[] args) {
        System.out.println("Count of subset sum: " + 
                          countSubsets(new int[]{1, 1, 2, 3}, 4));
        // Output: 3
        
        System.out.println("Count of subset sum: " + 
                          countSubsets(new int[]{1, 2, 7, 1, 5}, 9));
        // Output: 3
    }
}
```

**Time Complexity:** O(N × S)  
**Space Complexity:** O(N × S)

**DP Table:**
```
Input: {1, 1, 2, 3}, Sum = 4

     s: 0  1  2  3  4
i=0:    1  1  0  0  0  (num[0]=1)
i=1:    1  2  1  0  0  (num[1]=1)
i=2:    1  2  3  2  1  (num[2]=2)
i=3:    1  2  3  4  3  (num[3]=3)
                    ↑
               Answer: 3 ways
```

### Space Optimized O(S) Solution

```java
public class CountOfSubsetSumOptimized {
    
    public static int countSubsets(int[] num, int sum) {
        int n = num.length;
        int[] dp = new int[sum + 1];
        dp[0] = 1;
        
        // With only one number
        for (int s = 1; s <= sum; s++) {
            dp[s] = (num[0] == s) ? 1 : 0;
        }
        
        // Process all numbers (reverse iteration!)
        for (int i = 1; i < n; i++) {
            for (int s = sum; s >= 0; s--) {
                if (s >= num[i]) {
                    dp[s] += dp[s - num[i]];
                }
            }
        }
        
        return dp[sum];
    }
    
    public static void main(String[] args) {
        System.out.println("Count of subset sum: " + 
                          countSubsets(new int[]{1, 1, 2, 3}, 4));
        System.out.println("Count of subset sum: " + 
                          countSubsets(new int[]{1, 2, 7, 1, 5}, 9));
    }
}
```

**Time Complexity:** O(N × S)  
**Space Complexity:** O(S)

---

## Target Sum

**Problem:** Assign + or - to each number to make the sum equal to target S.

**Example:**
```
Input: {1, 1, 2, 3}, S = 1
Output: 3
Explanation: +1-1-2+3, -1+1-2+3, +1+1+2-3
```

**Key Insight:** This reduces to Count of Subset Sum!

If we denote positive subset as `sum1` and negative as `sum2`:
```
sum1 - sum2 = S
sum1 + sum2 = Sum(num)

Adding both equations:
2 × sum1 = S + Sum(num)
sum1 = (S + Sum(num)) / 2
```

So we need to find count of subsets with sum = `(S + Sum(num)) / 2`

```java
public class TargetSum {
    
    public static int findTargetSubsets(int[] num, int s) {
        int totalSum = 0;
        for (int n : num) {
            totalSum += n;
        }
        
        // If s + totalSum is odd or s > totalSum, no solution
        if (totalSum < s || (s + totalSum) % 2 != 0) {
            return 0;
        }
        
        return countSubsets(num, (s + totalSum) / 2);
    }
    
    private static int countSubsets(int[] num, int sum) {
        int n = num.length;
        int[][] dp = new int[n][sum + 1];
        
        // Populate sum=0 columns
        for (int i = 0; i < n; i++) {
            dp[i][0] = 1;
        }
        
        // With only one number
        for (int s = 1; s <= sum; s++) {
            dp[0][s] = (num[0] == s) ? 1 : 0;
        }
        
        // Process all subsets
        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= sum; s++) {
                dp[i][s] = dp[i - 1][s];
                if (s >= num[i]) {
                    dp[i][s] += dp[i - 1][s - num[i]];
                }
            }
        }
        
        return dp[n - 1][sum];
    }
    
    public static void main(String[] args) {
        System.out.println("Count of target sum: " + 
                          findTargetSubsets(new int[]{1, 1, 2, 3}, 1));
        // Output: 3
        
        System.out.println("Count of target sum: " + 
                          findTargetSubsets(new int[]{1, 2, 7, 1}, 9));
        // Output: 2
    }
}
```

**Time Complexity:** O(N × S)  
**Space Complexity:** O(N × S)

---

## Summary

The 0/1 Knapsack pattern teaches us:

1. **Start with Brute Force:** Understand the problem recursively
2. **Identify Overlapping Subproblems:** Look for repeated calculations
3. **Apply Memoization:** Cache results in a table
4. **Build Bottom-Up:** Construct solution iteratively
5. **Optimize Space:** Reduce dimensions when possible

**Key Formula for 0/1 Knapsack:**
```
dp[i][c] = max(
    dp[i-1][c],                    // exclude item i
    profit[i] + dp[i-1][c-weight[i]] // include item i
)
```

**Common Variations:**
- Equal Subset Sum → Find subset with sum = Total/2
- Subset Sum → Find subset with given sum
- Min Difference → Find two subsets with min difference
- Count Subsets → Count ways to achieve sum
- Target Sum → Assign +/- to reach target

All follow the same DP pattern with slight modifications!

---

## Practice Problems

1. Implement 0/1 Knapsack with item selection tracking
2. Solve Partition Equal Subset Sum (LeetCode 416)
3. Solve Target Sum (LeetCode 494)
4. Implement space-optimized versions of all problems
5. Add input validation and edge case handling

**Happy Coding!** 🚀
