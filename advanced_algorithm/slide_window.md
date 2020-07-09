# 滑动窗口

## 模板

```cpp
/* 滑动窗口算法框架 */
void slidingWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;

    int left = 0, right = 0;
    int valid = 0;
    while (right < s.size()) {
        // c 是将移入窗口的字符
        char c = s[right];
        // 右移窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        /*** debug 输出的位置 ***/
        printf("window: [%d, %d)\n", left, right);
        /********************/

        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // d 是将移出窗口的字符
            char d = s[left];
            // 左移窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}
```

需要变化的地方

- 1、右指针右移之后窗口数据更新
- 2、判断窗口是否要收缩
- 3、左指针右移之后窗口数据更新
- 4、根据题意计算结果

## 示例

[minimum-window-substring](https://leetcode-cn.com/problems/minimum-window-substring/)

> 给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串

```c++
// 根据 need 中每个字符所需的出现的次数，检查 win 是否满足条件
bool checkWindow(unordered_map<char, int> &win, unordered_map<char, int> &need) {
    for (const auto &item : need) {
        if (win[item.first] < item.second) {
            return false;
        }
    }
    return true;
}

// 给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字符的最小子串。
string minWindow(string s, string t) {
    unordered_map<char, int> win, need;
    // 初始判断条件
    // 注意：这里 T 中可能出现重复的字符，所以我们要记录字符的个数。

    for (const auto &c : t) {
        ++need[c];
    }

    auto left = 0;
    auto right = -1;
    auto minLen = numeric_limits<int>::max();
    auto minBegin = -1;
    // 注意：一定要进行类型转换，否则该判断会有问题
    // 隐式类型转换，int转成size_type，后者为unsigned！
    // -1一转，直接溢出
    while (right < (int)s.size()) {
        // right右移
        // 如果当前字符在字符串 T 中，增加计数
        if (need.find(s[++right]) != need.end()) {
            ++win[s[right]];
        }

        // 如果当前win满足条件，left尽可能左移
        while (checkWindow(win, need)) {
            // 更新最短长度及其起始位置
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                minBegin = left;
            }
            // 如果当前字符在 T 中，更新win计数
            if (need.find(s[left]) != need.end()) {
                --win[s[left]];
            }
            ++left;
        }
    }
    return minBegin == -1 ? "" : s.substr(minBegin, minLen);
}
```

[permutation-in-string](https://leetcode-cn.com/problems/permutation-in-string/)

> 给定两个字符串  **s1**  和  **s2**，写一个函数来判断  **s2**  是否包含  **s1 **的排列。

```c++
bool checkInclusion(string s1, string s2) {
    unordered_map<char, int> win, need;
    for (const auto &c : s1) {
        ++need[c];
    }
    auto left = 0;
    auto right = 0;
    auto matchNum = 0;
    while (right < s2.size()) {
        auto charToPush = s2[right++];
        if (need.find(charToPush) != need.end()) {
            ++win[charToPush];
            if (win[charToPush] == need[charToPush]) {
                ++matchNum;
            }
        }
        while (right - left >= s1.size()) {
            if (matchNum == need.size()) {
                return true;
            }
            auto charToPop = s2[left++];
            if (need.find(charToPop) != need.end()) {
                if (win[charToPop] == need[charToPop]) {
                    --matchNum;
                }
                --win[charToPop];
            }
        }
    }
    return false;
}
```

[find-all-anagrams-in-a-string](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

> 给定一个字符串  **s **和一个非空字符串  **p**，找到  **s **中所有是  **p **的字母异位词的子串，返回这些子串的起始索引。

```c++
vector<int> findAnagrams(string s, string p) {
    unordered_map<char, int> win, need;
    for (const auto &c : p) {
        ++need[c];
    }

    vector<int> ret;
    auto left = 0;
    auto right = 0;
    auto matchNum = 0;
    while (right < s.size()) {
        auto charToPush = s[right++];
        if (need.find(charToPush) != need.end()) {
            ++win[charToPush];
            if (need[charToPush] == win[charToPush]) {
                ++matchNum;
            }
        }

        while (right - left >= p.size()) {
            if (matchNum == need.size() && right - left == p.size()) {
                ret.push_back(left);
            }
            auto charToPop = s[left++];
            if (need.find(charToPop) == need.end()) {
                continue;
            }

            if (win[charToPop] == need[charToPop]) {
                --matchNum;
            }
            --win[charToPop];
        }
    }
    return ret;
}
```

[longest-substring-without-repeating-characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

> 给定一个字符串，请你找出其中不含有重复字符的   最长子串   的长度。
> 示例  1:
>
> 输入: "abcabcbb"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

```c++
int lengthOfLongestSubstring(string s) {
    unordered_set<char> win;
    auto left = 0;
    auto right = 0;
    auto maxLen = 0;
    while (right < s.size()) {
        auto charToPush = s[right++];
        if (win.find(charToPush) == win.end()) {
            win.insert(charToPush);
            maxLen = max(maxLen, right - left);
            continue;
        }
        while (right >= left) {
            auto charToPop = s[left++];
            // 左指针右移
            // 剔除重复字符之前的字符
            if (charToPush == charToPop) {
                break;
            }
            win.erase(charToPop);
        }
    }
    return maxLen;
}
```

## 总结

- 和双指针题目类似，更像双指针的升级版，滑动窗口核心点是维护一个窗口集，根据窗口集来进行处理
- 核心步骤
  - right 右移
  - 收缩
  - left 右移
  - 求结果

## 练习

- [ ] [minimum-window-substring](https://leetcode-cn.com/problems/minimum-window-substring/)
- [ ] [permutation-in-string](https://leetcode-cn.com/problems/permutation-in-string/)
- [ ] [find-all-anagrams-in-a-string](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)
- [ ] [longest-substring-without-repeating-characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)
