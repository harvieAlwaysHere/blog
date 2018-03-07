---
title: 剑指Offer算法记录(二)算法
tags:
  - 算法
date: 2018-03-02 17:06:07
categories: ItEassy
---
![](/images/offer.png)
### 本文主要记录阅读剑指Offer这本书所遇到的算法解释并作一些个人的拓展
---
### 第二章
#### 2.4 算法和数据操作
**排序和查找**是面试时考查算法的重点，如二分查找、归并排序、快速排序等。
**递归和循环**是两种算法实现的方式，基于递归的实现较整洁但性能不如基于循环实现的方法，我们应该根据题目特点来选择使用哪种实现方式。
**位运算**可以看做一类特殊的算法，共有**与、或、异或、左移和右移**五种位运算
---

#### 2.4.1 查找和排序
查找不外乎：顺序查找、二分查找、哈希表查找和二叉排序树查找，无论用递归还是循环都需要掌握这些查找方法的实现。
排序则复杂一些：插入排序、冒泡排序、归并排序、快速排序等不同算法的优劣，能够从**额外空间消耗、平均时间复杂度和最差时间复杂度等**方面去比较它们的优缺点。
值得一提的是，**快速排序**的实现代码是面试官钟爱的。
快速排序思路:选择一个数字(有多种选择法)，接下来把数组中的数字分成两部分，比选择的数字小的数字移到数组的左边，比选择数字大的数字移到数组右边
{% codeblock %}
int Partition(int data[], int length, int start, int end)//分区
{
  if(data==NULL || length<=0 || start<0 || end>=length)  //输入参数有效行检验
    throw new std::exception("Invalid Parameters");
 
  int index = RandomInRange(start,end);  //函数RandomInRange生成[start,end]的随机数index为基准数
  Swap(&data[index], &data[end]);        //将基准数放在最后

  int small = start-1;     //small位置表示小于基准数的值要填入的位置
  for(index=start; index<end; index++)  //从start开始寻找小于基准数的值
  {
    if(data[index]<data[end])     //如果找到一个小于基准数的值
    {
      small++;      //先small++锁定数组的前部分位置
      if(small!=index) Swap(&data[index], &data[small]);   
      //若small位置等于index位置，证明index位置的值是处于正确位置上不需要交换
      //否则证明index位置上的值小于基准数还不处于small位置上，需要交换到small位置上
    }
  }

  small++;  
  Swap(&data[small], &data[end]);  //最后将基准数与small++位置上的值交换，分区完成

  return small; //返回基准数的位置，此时small左边为小于基准数的值，small右边为大于基准数的值
}
//递归调用分区函数即为快速排序
void QuickSort(int data[],int length,int,start,int end)
{
  if(start==end) return;   //直到分区为一个数即为排好序了
  
  int index=Partition(data,length,start,end);
  if(index>start) QuickSort(data,length,start,index-1);
  if(index<end) QuickSort(data,length,index+1,end);
}
{% endcodeblock %}
