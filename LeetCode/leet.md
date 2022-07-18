给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。
链接：https://leetcode.cn/problems/two-sum

```html
示例 1：
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```



```js
var twoSum = function(nums, target) {

    for(let i = 0 ; i < nums.length ; i++){
        for(let x = i+1 ; x < nums.length ; x++){
            if(nums[i] + nums[x] === target){
                return [i,x]
            }
        }
    }

};

console.log(twoSum([3,2,7,4,2,6],10))
```

逻辑：双重循环 ，拿当前值去和所有值进行相加判断是否等于指定值（target）第二次循环的下标要加1 这样才能避免和自己相加 
误区：如果当前值和所有值 相加都不等于指定值 所在的任何值 和 当前值相加也不等于 指定值 