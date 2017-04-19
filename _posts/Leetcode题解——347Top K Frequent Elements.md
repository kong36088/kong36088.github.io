title: Leetcode题解——347Top K Frequent Elements
categories: LeetCode##分类
tags: [C++,LeetCode]##标签，多标签格式为 [tag1,tag2,...]
keywords: C++,LeetCode##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 解题记录
date: 2016/08/14 14:24:25 
---

# 题目

题目地址：[347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

Given a non-empty array of integers, return the k most frequent elements.

For example,
Given `[1,1,1,2,2,3]` and `k = 2`, return `[1,2]`.

Note: 
You may assume k is always valid, 1 ≤ k ≤ number of unique elements.
Your algorithm's time complexity must be better than O(n log n), where n is the array's size.

根据题目大意就是，给出一个数组，找出其中出现次数前K多的数字并以数组形式返回

<!--more-->

# 题解

## 利用优先队列

``` c++
class Solution {
private :
	unordered_map<int, int>um;
	vector<int>n;
	int numsSize;
public:
	vector<int> topKFrequent(vector<int>& nums, int k) {
		numsSize = nums.size();
		n = nums;
		priority_queue<pair<int,int>>pq;
		vector<int>result;
		//利用hashmap进行个数的统计
		for (int i = 0; i < numsSize; i++) {
			um[n[i]]++;
		}
		auto it = um.begin();
		//新建一个优先队列，优先队列是堆排序的升级版O(nlog2n)的时间复杂度，默认大顶堆
		for (int size = um.size(),i = 0; i < size; i++) {
			pq.push(make_pair(it->second, it->first));
			it++;
		}
		//最后取堆顶
		for (int i = 0; i < k; i++) {
			result.push_back(pq.top().second);
			pq.pop();
		}
		
		return result;
	}
};
```

大顶堆的特性是从顶部元素往下依次从大到小进行排序，小顶堆则反之
要取得前K个出现次数最多的数，只需要每次都取堆顶元素->删除堆顶->调整堆继续取堆顶

## 利用Bucket Sort

利用桶排序中桶号作为出现次数，桶中放入次数对应的数字

``` java
public List<Integer> topKFrequent(int[] nums, int k) {

	List<Integer>[] bucket = new List[nums.length + 1];
	Map<Integer, Integer> frequencyMap = new HashMap<Integer, Integer>();

	for (int n : nums) {
		frequencyMap.put(n, frequencyMap.getOrDefault(n, 0) + 1);
	}

	for (int key : frequencyMap.keySet()) {
		int frequency = frequencyMap.get(key);
		if (bucket[frequency] == null) {
			bucket[frequency] = new ArrayList<>();
		}
		bucket[frequency].add(key);
	}

	List<Integer> res = new ArrayList<>();

	for (int pos = bucket.length - 1; pos >= 0 && res.size() < k; pos--) {
		if (bucket[pos] != null) {
			res.addAll(bucket[pos]);
		}
	}
	return res;
}
```