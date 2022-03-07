### 最小的K个数

> 输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。  

示例 1：

```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```

示例 2：

```
输入：arr = [0,1,2,1], k = 1
输出：[0]
```


限制：

- 0 <= k <= arr.length <= 10000
- 0 <= arr[i] <= 10000

#### 排序后取前K个

```cpp
class Solution {
public:
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        vector<int> ans;
        sort(arr.begin(), arr.end());
        for (int i = 0; i < k; i++) {
            ans.push_back(arr[i]);
        }
        return ans;
    }
};
```

#### 利用大根堆

```cpp
class Solution {
public:
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        vector<int> ans;
        if (k == 0) return ans;
        // 默认是大根堆
        priority_queue<int> pq;
        for (int i = 0; i < k; i++) {
            pq.push(arr[i]);
        }
        // 如果后面的元素比堆中的最大值要大
        for (int i = k; i < arr.size(); i++) {
            if (pq.top() > arr[i]) {
                pq.pop();
                pq.push(arr[i]);
            }
        }
        // 将堆中的元素写入答案
        for (int i = 0; i < k; i++) {
            ans.push_back(pq.top());
            pq.pop();
        }
        return ans;
    }
};
```

#### 快排思想

快排不就是确定一个基准，然后比它小的都在左边，比它大的都在右边。如果这个基准就在第 K 个位置，那么就找到了，如果比 K 大，说明那个应该出现在第 K 个位置的基准在左边；如果比 K 小，说明那个应该出现在第 K 个位置的基准在右边。

```cpp
class Solution {
private:
    vector<int> quicksort(vector<int> &arr, int k, int left, int right)
    {
        int i = left, j = right;
        while (i < j) {
            // 找到右边第一个小于基准值的
            while (i < j && arr[j] >= arr[left]) j--;
            // 找到左边第一个大于基准值的
            while (i < j && arr[i] <= arr[left]) i++;
            // 交换
            swap(arr[i], arr[j]);
        }
        // 将基准设置到第 i 个位置
        swap(arr[i], arr[left]);
        // 如果比 K 大，说明那个应该出现在第 K 个位置的基准在左边
        if (i > k) return quicksort(arr, k, left, i - 1);
        // 如果比 K 小，说明那个应该出现在第 K 个位置的基准在右边
        if (i < k) return quicksort(arr, k, i + 1, right);
        // 找到了
        vector<int> ans;
        ans.assign(arr.begin(), arr.begin() + k);
        return ans;
    }
public:
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        if (k >= arr.size())    return arr;
        return quicksort(arr, k, 0, arr.size() - 1);
    }
};
```

### 数组中的第 K 个最大元素

> 给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。
>
> 请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。
>

示例 1:

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

示例 2:

```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```


提示：

- 1 <= k <= nums.length <= 10^4
- -10^4 <= nums[i] <= 10^4

#### 第 nums.size() - k 小的元素

同上

#### 优先队列

保存最大的 K 个元素在队列中，队首是最小值（小根堆），需要设置排序方式为 `greater<int>` ，如果后续元素比队首要大，就替换掉队首。

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        // 自定义小根堆，队首是队列中的最小值
        // 默认排序方式是 less<int>
        priority_queue<int, vector<int>, greater<int>> pq;
        // 初始化
        for (int i = 0; i < k; i++) {
            pq.push(nums[i]);
        }
        // 如果比队首大，就替换掉
        for (int i = k; i < nums.size(); i++) {
            if (nums[i] > pq.top()) {
                pq.pop();
                pq.push(nums[i]);
            }
        }
        // 返回队首，是这 K 个元素中的最小值，就是第 K 大的元素
        return pq.top();
    }
};
```

#### 快排思想

```cpp
class Solution {
public:
    int qortsort(vector<int> &nums, int k, int left, int right)
    {
        int i = left, j = right;
        while (i < j) {
            while (i < j && nums[j] >= nums[left])  j--;
            while (i < j && nums[i] <= nums[left])  i++;
            swap(nums[i], nums[j]);
        }
        swap(nums[i], nums[left]);
        if (i > k)  return qortsort(nums, k, left, i - 1);
        if (i < k)  return qortsort(nums, k, i + 1, right);
        return nums[i];
    }
    int findKthLargest(vector<int>& nums, int k) {
        return qortsort(nums, nums.size() - k, 0, nums.size() - 1);
    }
};
```

#### 优先队列

```cpp
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        auto cmp = [&nums1, &nums2](const pair<int, int> &a, const pair<int, int> &b) {
            return nums1[a.first] + nums2[a.second] > nums1[b.first] + nums2[b.second];
        };
        int m = nums1.size(), n = nums2.size();
        vector<vector<int>> ans;
        // 自定义比较，依葫芦画瓢
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> pq(cmp);
        for (int i = 0; i < min(k, m); i++) pq.emplace(i, 0);
        while (k-- > 0 && !pq.empty()) {
            auto [x, y] = pq.top();
            pq.pop();
            ans.emplace_back(initializer_list<int>{nums1[x], nums2[y]});
            if (y + 1 < n)  pq.emplace(x, y + 1);
        }
        return ans;
    }
};
```

