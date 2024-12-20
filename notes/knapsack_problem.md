# Knapsack Problem

## Reference
- [Leetcode notes - DP Knapsack Problem](https://blog.csdn.net/qq_42898642/article/details/135432027)
!
- [Introduction to Knapsack Problem, its Types and How to solve them](https://www.geeksforgeeks.org/introduction-to-knapsack-problem-its-types-and-how-to-solve-them/)

Note that there are differences in the English and Chinese translations of the knapsack problem type.

|English|Chinese|
|-|-|
|Fractional Knapsack Problem| N/A(不需要动态规划求解，不被视为背包问题)|
|0/1 Knapsack Problem|0/1 背包问题|
|Bounded Knapsack Problem|0/1 背包问题(被视为一种特殊情况，即在0/1背包问题的基础上添加了新的一个维度的限制)|
|Unbounded Knapsack Problem|完全背包问题|

## 0/1 Knapsack Problem
- [LeetCode 416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/description/)

  ```cpp
    bool canPartition(vector<int>& nums) {
        int sum = std::reduce(nums.begin(), nums.end());
        if (sum % 2 == 1){
            return false;
        }

        int target = sum / 2;
        // Pick from top i items to see if we can achieve j
        // dp[i,j] = dp[i-1,j] || dp[i-1,j-w_i]
        // With compression:
        // dp[j] = dp[j] || dp[j-w_i]
        vector<int> dp(target + 1, 0);
        dp[0] = true;
        for (int i = 1; i <= nums.size(); ++i) {
            for (int j = target; j >= 1; --j) {
                if (j >= nums[i - 1]) {
                    dp[j] |= dp[j - nums[i - 1]];
                }
            }
        }

        return dp[target];
    }
  ```

- [LeetCode 474. Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/description/)
  
  ```cpp
    int findMaxForm(vector<string>& strs, int m, int n) {
        // Max size from top i strs which meet the m/n limitation
        // dp[i,j,k] = max(dp[i-1,j,k], dp[i,j-count0,k-count1]
        // With compression
        // dp[j,k] = max(dp[j,k], dp[j-count0,k-count1]
        unordered_map<int, pair<int,int>> count;
        int count0, count1;
        for (int i = 1; i <= strs.size(); ++i) {
            countString(strs[i - 1], count0, count1);
            count[i] = make_pair(count0, count1);
        }

        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (int i = 1; i <= strs.size(); ++i) {
            int zero = count[i].first;
            int one = count[i].second;
            for (int j = m; j >= 0; --j) {
                for (int k = n; k >= 0; --k) {
                    cout << i << ":" << j << " " << k << "|" << zero << " "<< one << endl;
                    if (j >= zero && k >= one) {
                        dp[j][k] = std::max(dp[j][k], dp[j - zero][k - one] + 1);
                        cout << dp[j][k] << endl;
                    }
                }
            }
        }

        return dp[m][n];
    }
  ```

- [LeetCode 1049. Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/description/)

  ```cpp
    int lastStoneWeightII(vector<int>& stones) {

        int sum = std::reduce(stones.begin(), stones.end());
        int target = sum / 2;
        vector<int> dp(target + 1, 0);
        for(int i = 0; i < stones.size(); ++i) {
            for(int j = target; j >= stones[i]; --j) {
                dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }

        return sum - 2 * dp[target];
    }
  ```

## Unbounded Knapsack Problem

- Convert unbounded knapsack problem to 0/1 knapsack proble by adding the same `k` items.

  ```cpp
    int solve(vector<pair<int, int>>& items, int v) {
        vector<vector<int>> dp(items.size() + 1, vector<int>(v + 1, 0));
        for(int i = 1; i <= items.size(); ++i) {
            for(int j = 1; j <= v; ++j) {
                for(int k = 0; k <= v / items[i - 1].first; ++k) {
                    if(j >= k * items[i - 1].first) {
                        dp[i][j] = max(dp[i][j], dp[i - 1][j - k * items[i - 1].first] + k * items[i - 1].second);
                    }
                }
            }
        }

        for(auto& arr : dp) {
            for(auto i : arr) {
                cout << i << " ";
            }
            cout << endl;
        }
        return dp[items.size()][v];
    }
  ```

