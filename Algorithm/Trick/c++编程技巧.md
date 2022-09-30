## 自定义排序

https://leetcode.cn/problems/sort-array-by-increasing-frequency/

```cpp
class Solution {
public:
    unordered_map<int, int> M;
    vector<int> frequencySort(vector<int>& nums) {
        for (int x: nums) M[x] ++;
        sort(nums.begin(), nums.end(), [&](const int& a, const int& b) {
            return M[a] < M[b] || M[a] == M[b] && a > b;
        });
        return nums;
    }
};
```

## 字符串查找

https://leetcode.cn/problems/string-rotation-lcci/

```cpp
class Solution {
public:
    bool isFlipedString(string s1, string s2) {
        return s1.length() == s2.length() && (s1+s1).find(s2) != string::npos;
    }
};
```