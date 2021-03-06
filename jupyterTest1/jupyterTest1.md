```python
# shift+Tab  显示工具提示

# Tab+shift+M  合并代码块
```

# 初始读取数据


```python
import pandas as pd   # 读取数据
train_df = pd.read_csv('C:/Users/14279/Desktop/NLP_news/train_set.csv',sep='\t')
train_df.head(10)  #查看前几行数据是否读入正确，默认为5行
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2</td>
      <td>2967 6758 339 2021 1854 3731 4109 3792 4149 15...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>11</td>
      <td>4464 486 6352 5619 2465 4802 1452 3137 5778 54...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>7346 4068 5074 3747 5681 6093 1777 2226 7354 6...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2</td>
      <td>7159 948 4866 2109 5520 2490 211 3956 5520 549...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3</td>
      <td>3646 3055 3055 2490 4659 6065 3370 5814 2465 5...</td>
    </tr>
    <tr>
      <td>5</td>
      <td>9</td>
      <td>3819 4525 1129 6725 6485 2109 3800 5264 1006 4...</td>
    </tr>
    <tr>
      <td>6</td>
      <td>3</td>
      <td>307 4780 6811 1580 7539 5886 5486 3433 6644 58...</td>
    </tr>
    <tr>
      <td>7</td>
      <td>10</td>
      <td>26 4270 1866 5977 3523 3764 4464 3659 4853 517...</td>
    </tr>
    <tr>
      <td>8</td>
      <td>12</td>
      <td>2708 2218 5915 4559 886 1241 4819 314 4261 166...</td>
    </tr>
    <tr>
      <td>9</td>
      <td>3</td>
      <td>3654 531 1348 29 4553 6722 1474 5099 7541 307 ...</td>
    </tr>
  </tbody>
</table>
</div>



__分析：__ 从上图显示的头10行数据，可以看出数据读入成功，可以进行下一步数据分析操作

# 新闻类型对应标签  
> {'科技': 0, '股票': 1, '体育': 2, '娱乐': 3, '时政': 4, '社会': 5,'教育': 6, '财经': 7, '家居': 8, '游戏': 9, '房产': 10, '时尚': 11, '彩票': 12, '星座': 13}



## 1. 句子长度分析


```python
%pylab inline
train_df['text_len'] = train_df['text'].apply(lambda x: len(x.split(' ')))  # 分析text对应的句子的长度情况
print(train_df['text_len'].describe())
```

    Populating the interactive namespace from numpy and matplotlib
    count    200000.000000
    mean        907.207110
    std         996.029036
    min           2.000000
    25%         374.000000
    50%         676.000000
    75%        1131.000000
    max       57921.000000
    Name: text_len, dtype: float64
    

__分析：__  
* 总共有200000条新闻
* 句子平均新闻数为907个
* 最少新闻类别只有2条新闻，最多新闻类别有57921条新闻

__可视化：__ 分布直方图  
[matplotlib.pyplot.hist(x,bins=None,range=None, density=None, bottom=None, histtype='bar', align='mid', log=False, color=None, label=None, stacked=False, normed=None)参数介绍](https://blog.csdn.net/ToYuki_/article/details/104114925)  



```python
_ = plt.hist(train_df['text_len'], bins=100000)   # 200000行数据分成100000个柱子
plt.xlabel('Text char count')
plt.title("Histogram of char count")
```




    Text(0.5, 1.0, 'Histogram of char count')




![png](output_9_1.png)



```python
_a = plt.hist(train_df['text_len'][:100000], bins=200000)   # 截取前100000行数据进行观察
plt.xlabel('Text char count')
plt.title("Graph1 Histogram of char count")
```




    Text(0.5, 1.0, 'Graph1 Histogram of char count')




![png](output_10_1.png)



```python
_b = plt.hist(train_df['text_len'][100000:], bins=200000)   # 截取后100000行数据进行观察
plt.xlabel('Text char count')
plt.title("Graph2 Histogram of char count")
```




    Text(0.5, 1.0, 'Graph2 Histogram of char count')




![png](output_11_1.png)


__分析：__  
* 由于数据量比较大，运算较慢，故分为前后各100000进行运算，可以看到大部分句子长度都集中在10000以内，且大部分句子的长度都小于5000

## 2. 新闻类别分布  
> {'科技': 0, '股票': 1, '体育': 2, '娱乐': 3, '时政': 4, '社会': 5,'教育': 6, '财经': 7, '家居': 8, '游戏': 9, '房产': 10, '时尚': 11, '彩票': 12, '星座': 13}


```python
train_df['label'].value_counts().plot(kind='bar')    # 计算类别label的统计数据
plt.title('News class count')
plt.xlabel("category")
```




    Text(0.5, 0, 'category')




![png](output_14_1.png)


__分析：__   
* 数据分布大致呈阶梯状，其中属于 ___科技类___ 的新闻数量最多，超过了35000，而关于 ___星座类___ 的新闻数量最少，不足5000  
-------------  
## 3. 字符分布统计  
[ sorted(L, key=lambda x:x[1]) ](https://www.cnblogs.com/zle1992/p/6271105.html)


```python
from collections import Counter
all_lines = ' '.join(list(train_df['text']))   # 将text的中的字符重新组合成一个序列（由元组构成的列表）
word_count = Counter(all_lines.split(" "))    # 通过空格对字符进行划分，进而统计序列中总的字符数
word_count = sorted(word_count.items(), key=lambda d:d[1], reverse = True)   # 按照元组中第二个元组进行排序

print(len(word_count))

print(word_count[0])

print(word_count[-1])
```

    6869
    ('3750', 7482224)
    ('3133', 1)
    

__分析:__  
* 从上面可以看出,该数据集共有6869个字符（不重复）  
* '3750'这个字符出现次数最多，共出现7482224次  
* '3133'这个字符只出现了1次


```python
from collections import Counter
train_df['text_unique'] = train_df['text'].apply(lambda x: ' '.join(list(set(x.split(' ')))))
all_lines = ' '.join(list(train_df['text_unique']))
word_count = Counter(all_lines.split(" "))
word_count = sorted(word_count.items(), key=lambda d:int(d[1]), reverse = True)

print(word_count[0])

print(word_count[1])

print(word_count[2])
```

    ('3750', 197997)
    ('900', 197653)
    ('648', 191975)
    

__分析：__  
* 从上面结果可以看出，在200000个新闻中，含有'3750'这个字符的新闻占了197997个  
* 含有'900'这个字符的新闻占了197653个  
* 含有'648'这个字符的新闻占了191975个  
* '3750'、'900'、'648'这三个字符很大概率是标点符号
---------------------------  
## 4. 习题


```python
from collections import Counter
all_lines = ' '.join(list(train_df['text']))   # 将text的中的字符重新组合成一个序列（由元组构成的列表）
word_count = Counter(all_lines.split(" "))    # 通过空格对字符进行划分，进而统计序列中总的字符数
word_count = sorted(word_count.items(), key=lambda d:d[1], reverse = True)   # 按照元组中第二个元组进行排序
i = 0
n = 0
while(i < len(word_count)):
    if(word_count[i][0] is '3750'|'900'|'648'):
        n += word_count[i][1]
    i=i+1
print('/n每篇新闻平均由{0}个句子构成'.format(n))


```
