```python
import heapq
a = [100, 5, 20, 18, 10, 200]
heapq.heapify(a)
print(a)
# 创建小顶堆
heapq.heappush(a, 20)
heapq.heappush(a, 9)
heapq.heappush(a, 10)
print(a)
# 返回堆顶元素
res = heapq.heappop(a)
print(a)
# 返回堆顶元素，并添加新元素
res = heapq.heapreplace(a, 300)
print(res)
print(a)
# 先添加，再弹出
res = heapq.heappushpop(a, 400)
print(res)
print(a)
a = [100, 5, 20, 18, 10, 200]
heapq.heapify(a)
print(a)
# 获取a中前3个最大的值
print(heapq.nlargest(3, a))
# 获取a中前3个最小的值
print(heapq.nsmallest(3, a))
# 默认不支持大顶堆，直接将元素改成负数，然后生成小顶堆，再取负数，结果就是大顶堆了
b = [100, 5, 20, 18, 10, 200]
b = [-item for item in b]
heapq.heapify(b)
b = [-item for item in b]
print(b)
```