---
title: 使用 Python3 爬取 Play 商店后台评论笔记记录
date: 2018-03-13 23:27:22
tags: [Python,爬虫]
categories: Python
draft: false
---

### Python selenium 三种等待方式

学习链接：https://huilansame.github.io/huilansame.github.io/archivers/sleep-implicitlywait-wait

项目使用以下方式
```
# -*- coding: utf-8 -*-
from selenium import webdriver
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

driver = webdriver.Firefox()
driver.implicitly_wait(10)  # 隐性等待和显性等待可以同时用，但要注意：等待的最长时间取两者之中的大者
driver.get('https://huilansame.github.io')
locator = (By.LINK_TEXT, 'CSDN')

try:
    WebDriverWait(driver, 20, 0.5).until(EC.presence_of_element_located(locator))
    print driver.find_element_by_link_text('CSDN').get_attribute('href')
finally:
    driver.close()
    
```

### Python 遍历 list
学习链接：https://blog.csdn.net/baidu_24545901/article/details/78729309

采用的方式：
```
temp = [{'h': 'u'}, {'h': 'a'}, {'h': 'u'}]
    for i, val in enumerate(temp):
        print(val['h'])
```

### Python3 词典数组操作

学习链接：https://blog.csdn.net/dance117/article/details/80304238

**定义一个空的词典数组**
```
review_data = {}
```
**增加**
```
review_data['review_content'] = 'very good'
review_data['review_rating'] = 5

print(review_data)

{'review_content':'very good', 'review_rating': 5}
```

**删除**
```
review_data['review_content'] = 'very good'
# 指定删除
del review_data['review_content']
# 全部删除
review_data.clear()
# 删除指定 key，且返回删除的key的值
review_data.pop('review_content')
```

**修改**
```
# 给指定的key重新赋值修改
review_data['name'] = 'jun'

# 使用 update() 方法
# 无重复key，相当于新插入
review_data = {'name': 'jun'}
review_data.update({'rating': 5})
print(review_data)  ---->   {'name': 'jun', 'rating': 5}

# 重复key，会覆盖旧的值
review_data = {'name': 'jun'}
review_data.update({'name': 'jun'})

print(review_data)  ---->   {'name': 'jun'}
```

**查询**
```
review_data = {'name': 'jun'}
reivew_data['name']
```

**两个字典合并**
```
dic1 = {'name': 'jun'}
dic2 = {'age': '18'}

dic1.update(dic2)
print(dic1)  ----> {'name': 'jun', 'age': '18'}
```


### 表格处理

官方文档：https://docs.python.org/3/library/csv.html  
参考链接：https://python3-cookbook.readthedocs.io/zh_CN/latest/c06/p01_read_write_csv_data.html

**读取表格数据**
```
with open('/Users/apple/Desktop/review.csv', newline='') as c:
    reader = csv.reader(c)
    for r in reader:
        print(r)
```

**写入数据**

```
# encoding='utf-8-sig' 防止写入中文乱码
with open('/Users/apple/Desktop/review.csv', 'w', newline='', encoding='utf-8-sig') as c:
    headers = ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume']
    rows = [{'Symbol': 'AA', 'Price': 39.48, 'Date': '6/11/2007',
             'Time': '9:36am', 'Change': -0.18, 'Volume': 181800},
            {'Symbol': 'AIG', 'Price': 71.38, 'Date': '6/11/2007',
             'Time': '9:36am', 'Change': -0.15, 'Volume': 195500},
            {'Symbol': 'AXP', 'Price': 62.58, 'Date': '6/11/2007',
             'Time': '9:36am', 'Change': -0.46, 'Volume': 935000},
            ]
    c_w = csv.DictWriter(c, headers)
    c_w.writeheader()
    c_w.writerows(rows)
```
