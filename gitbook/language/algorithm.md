#### 递归算法

递归的特点：

调用自身 + 结束条件

~~~python
def func4():
    if x>0:
        print(x)
        func4(x-1)
~~~

#### 汉诺塔问题

~~~python
#n是n个盘子,a,b,c是柱子的名字,
#原函数意思是，n个盘子从a,经过b,移动到c,目的是移动到c
def hanoi(n,a,b,c):
	if n > 0:
		hanoi(n-1,a,c,b)  #第1步，把n-1个盘子从A经过C移动到B
		print("moving from %s to %s"%(a,c)) #第2步，把第n个盘子从A移动到C
		hanoi(n-1,b,a,c)  #第3步，把n-1个盘子从B经过A移动到C

hanoi(3,'a','b','c')
~~~

#### 查找

##### 顺序查找 

~~~python
def liner_search(li, val):
    for ind, v in enumerate(li):
        if v == val:
            print("found")
            return ind
    else:
        print("no found")
        return None
ls = [1, 2, 3, 4]
print(liner_search(ls, 3))
~~~



##### 二分法

~~~python
def binary_search(li, val):
    left = 0
    right = len(li) - 1
    while left <= right:
        mid = (left + right) // 2
        if li[mid] == val:
            return mid
        elif li[mid] > val:
            right = mid - 1
        else:
            left = mid + 1
    else:
        return None
li = [1,2,3,4,5,6,7,8,9]

print(binary_search(li, 7))
~~~



#### 排序

最垃圾的三个：

冒泡排序，选择排序，插入排序

比较好的三个：

快速排序，堆排序，归并排序

其他排序：

希尔排序，计数排序，基数排序

##### 冒泡排序 n^2

[![p9YBBvQ.png](https://s1.ax1x.com/2023/05/03/p9YBBvQ.png)](https://imgse.com/i/p9YBBvQ)

比较7是否大于5，如果是则交换，同理

第一趟结束后会把最大的排在最上面，所以我们只要运行n - 1趟就可以排好

[![p9YB25V.png](https://s1.ax1x.com/2023/05/03/p9YB25V.png)](https://imgse.com/i/p9YB25V)

~~~python
def bubble_sort(li):
    for i in range(len(li) - 1):
        for j in range(len(li) - 1 - i):
            if li[j] > li[j + 1]:
                li[j],li[j+1] = li[j+1],li[j]

        print(li)
li = [3,6,8,9,2,4,5,7,1]
bubble_sort(li)
# [3, 6, 8, 2, 4, 5, 7, 1, 9]
# [3, 6, 2, 4, 5, 7, 1, 8, 9]
# [3, 2, 4, 5, 6, 1, 7, 8, 9]
# [2, 3, 4, 5, 1, 6, 7, 8, 9]
# [2, 3, 4, 1, 5, 6, 7, 8, 9]
# [2, 3, 1, 4, 5, 6, 7, 8, 9]
# [2, 1, 3, 4, 5, 6, 7, 8, 9]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
~~~

##### 选择排序

比较low的选择排序：

将一个列表，从第一位开启往后找找到一个最小的数放在一个新列表的第一位，将这个列表剩下的n - 1的数继续找，再找到最小的数，放在新列表的第二位，以此类推，就可以排序；

一般的选择排序：

将一个列表，从第一位开启往后找找到一个最小的数，和列表的第一位数进行交换，同理，从第二位往后开始再找到最小的和第二位交换，也是需要n - 1趟

~~~python
def select_sort(li):
    for i in range(len(li) - 1):
        min_loc = i
        for j in  range(i+1, len(li)):
            if li[j] < li[min_loc]:
                min_loc = j
        li[i], li[min_loc] = li[min_loc], li[i]
        print(li)

li = [3,4,7,6,5,2,9,1,8]
print(li)
select_sort(li)

# [3, 4, 7, 6, 5, 2, 9, 1, 8]
# [1, 4, 7, 6, 5, 2, 9, 3, 8]
# [1, 2, 7, 6, 5, 4, 9, 3, 8]
# [1, 2, 3, 6, 5, 4, 9, 7, 8]
# [1, 2, 3, 4, 5, 6, 9, 7, 8]
# [1, 2, 3, 4, 5, 6, 9, 7, 8]
# [1, 2, 3, 4, 5, 6, 9, 7, 8]
# [1, 2, 3, 4, 5, 6, 7, 9, 8]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
~~~



##### 插入排序

~~~python
def insert_sort(li):
    for i in range(1, len(li)): # i是摸到的牌的下标
        tmp = li[i]
        j = i - 1   # j是手里的牌的下标
        while j >= 0 and li[j] > tmp:
            li[j+1] = li[j]
            j = j - 1
        li[j+1] = tmp
        print(li)
li = [9,8,7,6,5,4,3,2,1]
insert_sort(li)

# [8, 9, 7, 6, 5, 4, 3, 2, 1]
# [7, 8, 9, 6, 5, 4, 3, 2, 1]
# [6, 7, 8, 9, 5, 4, 3, 2, 1]
# [5, 6, 7, 8, 9, 4, 3, 2, 1]
# [4, 5, 6, 7, 8, 9, 3, 2, 1]
# [3, 4, 5, 6, 7, 8, 9, 2, 1]
# [2, 3, 4, 5, 6, 7, 8, 9, 1]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
~~~



##### 快速排序

n*logn

[![p9YslgU.png](https://s1.ax1x.com/2023/05/03/p9YslgU.png)](https://imgse.com/i/p9YslgU)

~~~python
def partition(li, left, right):
    tmp = li[left]
    while left < right:
        while left < right and li[right] >= tmp:    # 从右边找比tmp小的数
            right -= 1  # 如果大于就往左走一步
        li[left] = li[right] #  把右边的值写到左边空位上
        print(li, "right")
        while left < right and li[left] <= tmp: # 再判断下如果从右边替换过来的数比tmp小的话，就往右走一步
            left += 1
        li[right] = li[left]    #   把左边的值写在右边
        print(li, 'left')
    li[left] = tmp  #   把tmp归位
    return left
def quick_sort(li, left, right):
    if left < right:
        mid = partition(li, left, right)
        quick_sort(li, left, mid - 1)
        quick_sort(li, mid + 1, right)
li = [5, 7, 4, 6, 3, 1, 2, 9, 8]
print(li)
quick_sort(li, 0, len(li) -1)
print(li)
# [5, 7, 4, 6, 3, 1, 2, 9, 8]
# [2, 7, 4, 6, 3, 1, 2, 9, 8] right
# [2, 7, 4, 6, 3, 1, 7, 9, 8] left
# [2, 1, 4, 6, 3, 1, 7, 9, 8] right
# [2, 1, 4, 6, 3, 6, 7, 9, 8] left
# [2, 1, 4, 3, 3, 6, 7, 9, 8] right
# [2, 1, 4, 3, 3, 6, 7, 9, 8] left
# [1, 1, 4, 3, 5, 6, 7, 9, 8] right
# [1, 1, 4, 3, 5, 6, 7, 9, 8] left
# [1, 2, 3, 3, 5, 6, 7, 9, 8] right
# [1, 2, 3, 3, 5, 6, 7, 9, 8] left
# [1, 2, 3, 4, 5, 6, 7, 9, 8] right
# [1, 2, 3, 4, 5, 6, 7, 9, 8] left
# [1, 2, 3, 4, 5, 6, 7, 9, 8] right
# [1, 2, 3, 4, 5, 6, 7, 9, 8] left
# [1, 2, 3, 4, 5, 6, 7, 8, 8] right
# [1, 2, 3, 4, 5, 6, 7, 8, 8] left
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
~~~

最坏情况：

~~~
9，8，7，6，5，4，3，2，1
~~~

为了避免最坏情况出现，我们可以随机选择一位数和第一位数进行交换



##### 堆排序

树

[![p9tCa2n.png](https://s1.ax1x.com/2023/05/04/p9tCa2n.png)](https://imgse.com/i/p9tCa2n)

二叉树-树的度为2的树（左孩子节点和右孩子节点）

满二叉树和完全二叉树

[![p9tC654.png](https://s1.ax1x.com/2023/05/04/p9tC654.png)](https://imgse.com/i/p9tC654)

[![p9tPuoF.png](https://s1.ax1x.com/2023/05/04/p9tPuoF.png)](https://imgse.com/i/p9tPuoF)

堆是一种特殊的完全二叉树

[![p9tCvsP.png](https://s1.ax1x.com/2023/05/04/p9tCvsP.png)](https://imgse.com/i/p9tCvsP)

堆的向下调整：

这样不是一个大根堆，所以我们要把这个2换下去，让9上去

[![p9tPJL6.png](https://s1.ax1x.com/2023/05/04/p9tPJL6.png)](https://imgse.com/i/p9tPJL6)

[![p9tP0Wd.png](https://s1.ax1x.com/2023/05/04/p9tP0Wd.png)](https://imgse.com/i/p9tP0Wd)

挨个出数

如果9要下去，那么我们暂时让最下面的3顶上去，8大于3，所以8上去，6替代8的位置，因为6上去了，所以4替代6的位置，而3去替换原来4的位置

[![p9tigj1.png](https://s1.ax1x.com/2023/05/04/p9tigj1.png)](https://imgse.com/i/p9tigj1)

构造堆

[![p9tFY5D.png](https://s1.ax1x.com/2023/05/04/p9tFY5D.png)](https://imgse.com/i/p9tFY5D)[![p9tFJUO.png](https://s1.ax1x.com/2023/05/04/p9tFJUO.png)](https://imgse.com/i/p9tFJUO)

堆排序的过程

~~~
1.建立堆-构造堆
2.得到堆顶元素，为最大元素
3.去掉堆顶，将堆的最后一个元素放在堆顶，此时可以通过一次调整重新使得堆有序
4.堆顶元素为第二大元素
5.重复3步骤，直到堆为空
~~~

时间复杂度：n * logn

~~~python
def sift(li, low, high):
    """

    :param li: 列表
    :param low: 堆的根节点位置
    :param high: 堆的最后一个元素的位置
    :return:
    """
    i = low # i最开始指向根节点
    j = 2 * i + 1 # j开始是左孩子
    tmp = li[low] # 把堆顶存起来
    while j <= high: # 只要j位置有数
        if j + 1 <= high and li[j+1] > li[j]: # 如果右孩子有并且比较大
            j = j + 1   # j指向右孩子
        if li[j] > tmp:
            li[i] = li[j]
            i = j   #   往下看一层
            j = 2 * i + 1
        else:       # tmp更大，把tmp放在i的位置上
            li[i] = tmp # 把tmp放在某一层领导位置上
            break
    else:
        li[i] = tmp # 把tmp放在叶子节点上

def heap_sort(li):
    n = len(li)
    for i in range((n-2)//2, -1, -1):
        sift(li, i, n-1)
    for i in range(n-1, -1, -1):
        li[0], li[i] = li[i], li[0]
        sift(li, 0, i-1)




li = [i for i in range(100)]
import random
random.shuffle(li)

print(li)
heap_sort(li)
print(li)
~~~

















