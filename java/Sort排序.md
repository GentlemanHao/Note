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



### 2. 快速排序

通过一趟排序将要排序的数据分割成独立的两部分：**分割点左边都是比它小的数，右边都是比它大的数**。然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

https://blog.csdn.net/shujuelin/article/details/82423852

~~~java
    public static void quickSort(int[] arr, int start, int end) {
        if (start > end) return;
        int left = start;
        int right = end;
        int base = arr[left];

        while (left < right) {
            while (arr[right] >= base & left < right) {
                right--;
            }

            while (arr[left] <= base & left < right) {
                left++;
            }

            if (left < right) {
                swap(arr, left, right);
            }
        }
        swap(arr, start, left);

        quickSort(arr, start, left - 1);
        quickSort(arr, right + 1, end);
    }

    public static void swap(int[] arr, int p1, int p2) {
        int tmp = arr[p1];
        arr[p1] = arr[p2];
        arr[p2] = tmp;
    }

    public static void printArr(int[] arr) {
        for (int value : arr) {
            System.out.print(value + " ");
        }
        System.out.println();
    }
~~~



