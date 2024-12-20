# Knapsack Problem

## Reference
- [CSDN](https://blog.csdn.net/qq_42898642/article/details/135432027)

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
## Complete Knapsack Problem
