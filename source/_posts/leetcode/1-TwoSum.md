---
title: LeetCode 挑战 - Two Sum (1)
date: 2017-07-11 11:33:27
categories:
- LeetCode
tags:
- LeetCode
---
-----

> LeetCode 挑战 - [1. Two Sum](https://leetcode.com/problems/two-sum/#/description)

-----

### Description
Given an array of integers, return indices of the two numbers such that they add up to a specific target.  
You may assume that each input would have exactly one solution, and you may not use the same element twice.

#### Example:
```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

### Solutions (JavaScript)
* 时间复杂度 `O(n^2)`, 测试耗时: `1898 ms`，思路是使用计算差值，然后通过 `findeIndex` 找出所需数值下标
```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @param {number} lastIndex
 * @return {number[]}
*/
var twoSum = function(nums, target) {
    for (let i = 0; i < nums.length; i++) {
        const findNum = target - nums[i]
        const index = nums.findIndex(num => num === findNum)
        if (!!~index && index !== i) {
            return [i, index]
        }
    }
}
```

* 时间复杂度 `O(n)`，测试耗时 `102 ms`，思路：计算差值，并用 `o` 对象缓存遍历过数值，通过 `hasOwnProperty` 来匹配差值，效率远高于 `findeIndex`
```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @param {number} lastIndex
 * @return {number[]}
*/
var twoSum = function(nums, target) {
    var o = {}
    for (var i = 0; i < nums.length; i++) {
        var n = nums[i]
        if (o.hasOwnProperty(nums[i])) {
            return [o[n], i]
        }
        o[target - n] = i
    }
}
```
