最近学习如何写cuda核，发现自己真的需要好好学学c++了……刷力扣！

* https://leetcode.cn/problems/alert-using-same-key-card-three-or-more-times-in-a-one-hour-period/

```c++
class Solution {
public:
    pair<int, int> get_time(const string& s) {
        int h = (s[0] - '0') * 10 + (s[1] - '0');
        int m = (s[3] - '0') * 10 + (s[4] - '0');
        return {h, m};
    }
    bool larger_than_1(pair<int, int>& t1, pair<int, int>& t2) {
        return t2.first - t1.first > 1 || t2.first - t1.first == 1 && t2.second > t1.second;
    }
    vector<string> alertNames(vector<string>& keyName, vector<string>& keyTime) {
        unordered_map<string, int> M;
        vector< pair<int, int> > hm;
        for (string& x: keyTime) {
            hm.emplace_back(get_time(x));
        }
        vector<int> idx;
        for (int i=0; i<keyName.size(); i++) idx.emplace_back(i);
        sort(idx.begin(), idx.end(), [&](const int a, const int b) {
            return hm[a] < hm[b];
        });
        vector<string> alert;
        int left = 0;
        for (int i: idx) {
            while (larger_than_1(hm[idx[left]], hm[i])) {
                M[keyName[idx[left]]] --;
                left ++;
            }
            M[keyName[i]] ++;
            if (M[keyName[i]] >= 3) alert.emplace_back(keyName[i]);
            // cout <<keyName[idx[i]] <<" " <<keyTime[idx[i]] <<endl;
        }
        sort(alert.begin(), alert.end());
        alert.erase(unique(alert.begin(), alert.end()), alert.end());
        return alert;
    }
};
```