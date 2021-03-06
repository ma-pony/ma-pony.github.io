---
title: Mysql Index
// title 必须是英文
subtitle: mysql 索引
slug: mysql-index
tags: mysql

/* You can find the list of tags here https://github.com/Hashnode/support/blob/main/misc/tags.json
You need to upload your image to https://hashnode.com/uploader 
and use the uploaded image URL as COVER_IMAGE_URL */ 

cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1654873645238/7TFlE4Q8N.jpeg?auto=compress
domain: pony.hashnode.dev
---


# 索引
索引是一种使用查找键，能够使用户高效查找数据对数据结构
提升数据查询效率，减少数据扫描的量，随机IO转顺序IO
可以通过磁盘的访问次数来评估索引的性能

## 术语
索引文件和数据文件的存储单位都是block。
一个block中可能存在多条索引或者多条数据。
索引文件的block和数据文件的block是不同的。

### 顺序文件
所有数据文件被按照某一个字段的顺序排序后，所组成的文件
具体存储的数据文件

### 有序索引
按照查找键进行排序组成的索引文件

### 主键索引
查找键和排序键都是同一个的有序索引文件

### 二级索引
索引文件的查找键不是数据文件的排序键

### 索引顺序文件
存储主键索引的顺序索引文件

### 密集索引
每个排序键都会出现在索引文件中

### 稀疏索引
每个block提取一个数据建立一个索引
只有主键索引才能成为稀疏索引

### 多级索引
如果索引文件过大，查找困难，可以给索引文件再加一个索引
多级索引可以显著减少磁盘访问次数
然而具体要分多少级需要根据具体情况具体分析，而B+树就可以做到动态调节索引的层级

## 分类

### 按字段特性分类
1、主键索引
建立在主键之上，只会有一个
2、唯一索引
建立在UNIQUE字段之上
3、普通索引
建立在普通字段之上
4、前缀索引
只对字符类型字段的前几个字符或对二进制类型字段的前几个bytes建立的索引，而不是整个字段上建索引

### 按索引字段个数分类
1、单列索引
建立在单个列之上

2、联合索引
建立在多个列之上，又叫复合索引、组合索引

### 按数据与索引的存储关联性划分
索引的储存顺序和数据的存储顺序是否是关系的，有关就是聚簇索引，无关就是非聚簇索引
具体实现方式根据索引的数据结构不同会有所不同，都以B+数实现的索引举例

1、聚簇索引
Innodb的主键索引，非叶子节点存储的是索引指针，叶子节点存储的是既有索引也有数据，是典型的聚簇索引
索引和数据的存储顺序是强相关的

2、非聚簇索引
MyISAM中索引和数据文件分开存储，叶子节点存储的是数据存放的地址，而不是具体的数据，是典型的非聚簇索引
索引的存储和数据的存储顺序毫无关联


### 按底层数据结构划分
1、hash索引
检索效率非常高，一次定位
hash索引存储的是hash值，只能做等值的比较，不能用作范围查询
可能存在hash冲突

2、有序索引
B+数索引需要从根节点开始访问，需要多次IO访问

## B树和B+树
实际就实现了多级索引

### B+树
叶子节点的保存了下一个叶子节点的指针，同时保存了指向数据文件的指针
非叶子节点都保存了索引数据和指向下一层block的指针

他是个高度平衡的树，就是说每个叶子节点到根节点的高度都是相同的
他的插入和删除都会更改树的结构，

#### 中间节点的数据结构
节点是单独的一个block，包含多个查找键和其指向下一个block的指针
每个查找键前后都会有一个指针，有n个查找键，则会有n+1个指针

#### 叶子结点的数据结构
节点是单独的一个block，包含多个查找键及其指向数据文件的指针，
同时每一个block末尾会有指向下一个block的指针，构成一个block链表


## 基于hash的索引

经过hash函数处理后，可以直接定位到索引，获取到数据地址

基于哈希的索引，必然会存在hash冲突。
由于hash索引的每个block块大小都是固定的，当冲突剧烈时可能会存在溢出的情况，
溢出的数据会存在另一个block，与原本的block通过链表的形式连接，
冲突越多，链表越长。

这时候就需要重新构建hash索引来进行扩展。


## 多维索引

### 网格文件索引
典型的例子是地理位置经纬度

索引会建立成表格的样子，每一个单元格都有自己的横纵坐标，其中存储了相应的数据。

例如第一个单元格横坐标为0-10，纵坐标也为0-10，则经度0-10，纬度0-10的点都会存储在这个单元格中。
其中每一个横行的分割线我们称为Xm,纵列的分割线称为Yn,

但实际的索引文件存储的更加简单
首先存储的是横行分割线总数m和纵列分割线总数n，
其次是每个横行分割线所在的分割点的数值，即10，20，30等，
再是每个纵列分割线所在的分割点的数值，
最后存储的是每个单元格的数据依次排序，其中储存了指向具体数据文件的地址。

查询时只要确定了横纵列的数据，找到其对应的下标，就可确定其所在的单元格，查询到具体数据 。

### 多key索引
先按照第一个key建立索引文件，再按照第二个key建立索引文件

第一个key的索引文件指向第二个索引文件，第二个索引文件指向数据文件

### bitmap索引
位图索引

要求数据记录储存在文件中的位置是固定的。
已有数据x,id为2
则建立索引
键为x, 值为表数据总长度的bit串。
x: 00000000000000000
其中数据x的ID为2，则将第二位置为1
x: 01000000000000000

