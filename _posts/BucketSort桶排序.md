title: BucketSort桶排序
categories: 算法##分类
tags: [BucketSort,排序,算法]##标签，多标签格式为 [tag1,tag2,...]
keywords: BucketSort,排序,算法##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 桶排序个人解读
date: 2016/08/14 15:24:25 
---

# Bucket Sort介绍
桶排序 (Bucket sort)或所谓的箱排序，是一个排序算法，工作的原理是将数组分到有限数量的桶子里。每个桶子再个别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排序）。桶排序是鸽巢排序的一种归纳结果。当要被排序的数组内的数值是均匀分配的时候，桶排序使用线性时间（Θ（n））。但桶排序并不是 比较排序，他不受到 O(n log n) 下限的影响。

假定：输入是由一个随机过程产生的[0, 1)区间上均匀分布的实数。将区间[0, 1)划分为n个大小相等的子区间（桶），每桶大小1/n：[0, 1/n)， [1/n, 2/n)， [2/n, 3/n)，…，[k/n, (k+1)/n )，…将n个输入元素分配到这些桶中，对桶中元素进行排序，然后依次连接桶输入0 ≤A[1..n] <1辅助数组B[0..n-1]是一指针数组，指向桶（链表）。

介绍一个非常易于理解Bucket Sort的网站：[Bucket Sort](http://www.cs.usfca.edu/~galles/visualization/BucketSort.html)

桶排序有一个比较明显的缺点：
数据最好都处于一个比较小的区间内，如果数据处于1-2亿的区间当中，用桶排序显然不合适

![BucketSort](/uploads/BucketSort.jpg)

<!--more-->

# 实现

``` c++
class Solution {
public:
	vector<int> bucket(vector<int>& nums) {
		int maxValue = getMax(nums);
		int numsSize = nums.size();
		vector<vector<int>>bucket(numsSize + 1);
		//找桶：value*numsSize/(maxValue+1)
		for (int i = 0; i < numsSize; i++) {
			int bucketNum = (nums[i] * numsSize) / (maxValue + 1);
			bucket[bucketNum].push_back(nums[i]);
		}
		//按顺序遍历桶，生成结果
		for (int i = 0, m = 0; i < numsSize + 1; i++) {
			if (!bucket[i].empty()) {
				//如果桶的数字个数大于1，对桶内进行排序
				if (bucket[i].size() > 1) {
					sort(bucket[i].begin(), bucket[i].end());
				}
				for (int size = bucket[i].size(), j = 0; j < size; j++, m++) {
					nums[m] = bucket[i][j];
				}
			}
		}
		return nums;
	}
	int getMax(vector<int> &nums) {
		int size = nums.size();
		int max = INT_MIN;
		for (int i = 0; i < size; i++) {
			if (nums[i] > max) {
				max = nums[i];
			}
		}
		return max;
	}
};
```

