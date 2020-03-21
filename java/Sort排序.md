### 1. 冒泡排序 

**原理：**比较相邻的两个元素，将较大的元素交换至右端。

**思路：**依次比较相邻的两个元素，将小数放前边，大数放后边；第一趟比较完成后，最后一个数一定是数组中最大的一个数，所以第二趟比较的时候最后一个数不参与比较，依次类推，每一趟比较次数-1。

**外层循环控制排序趟数，内层循环控制每一趟排序次数。**

~~~java
public static void sort(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = 0; j < nums.length - i - 1; j++) {
                if (nums[j] > nums[j + 1]) {
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                }
            }
        }
    }
~~~

