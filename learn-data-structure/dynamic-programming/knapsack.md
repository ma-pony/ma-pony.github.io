背包最大容量V的东西，总共有n个东西，每个东西的体积是w，
而价格是p，假设每样东西只要一个，如何是背包的总价最大。

第i个东西只有两种选择，放或者不放进背包
如果不放进背包，那就只放置了i-1个东西，最大总价为c(i-1, v)
如果放进背包，那就放置了i个东西，那么留给前n-1个东西只有v-w[i]的容量了,最大总价为c(i-1, v-w[i])+p[i]
比较这两种选择的总价，取最大值

```python
v = 7 # 背包的容量，即列数
n = 3 # 东西的数量, 即行数
w = [2, 3, 4] # 东西的体积，即占几列
p = [3, 4, 5] # 东西的价格
c = [[0]*v for i in range(n)] # 初始化一个二维数组
# c = [[0 for i in range(v+1)] for j in range(n+1)]
```
w[i] 记录第i个东西的体积
p[i] 记录第i个东西的价格
c[i][v] 记录i个物体放入容量为v的背包的最大价值
方程为 c[i][v] = max(c[i-1][v], c[i-1][v-w[i]]+p[i])
```python
j = 0
# 填充第一行数据
while j < v:
    # j 表示当前的容量
    # 如果j小于第i个东西的体积，那么就不放入背包
    # 反之，就放入背包
    if j+1 < w[0]:
        c[0][j] = 0
    else:
        c[0][j] = p[0]
    
    j += 1

# 处理其他的行
for i in range(1, n):
    j = 0
    while j < v:
        if j + 1 < w[i]: # 放不下j+1体积的东西，直接取上一行的数据
            c[i][j] = c[i-1][j]
        elif j+1 == w[i]: # 正好放下, 那对比上一行数据，取价值最大的
            c[i][j] = max(p[i], c[i-1][j])
        else: # 可以放下，那就放下，然后比较上一行的数据
            c[i][j] = max(c[i-1][j], c[i-1][j-w[i]]+p[i])
        j += 1

print(c)

```
