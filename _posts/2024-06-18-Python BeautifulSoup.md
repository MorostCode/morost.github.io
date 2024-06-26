---
title: BeautifulSoup
published: true
date: 2024-06-18
update: 2024-06-20
tags:
  - Python
  - BeautifulSoup
---

# BeautifulSoup

## 简介

Beautiful Soup 是一个可以从 HTML 或 XML 中提取数据的 Python 库。

> Beautiful Soup 自动将输入文档转为 Unicode 编码，输出文档转为 UTF-8 编码。因此无需考虑编码方式。

## 安装

```bash
pip install bs4
pip install lxml
```

## 使用

### 解析器

Beautiful 在解析时依赖解析器，以下是一些支持的解析器：

```python
BeautifulSoup(markup, 'html.parser')
BeautifulSoup(markup, 'lxml')
BeautifulSoup(markup 'xml')
BeautifulSoup(markup, 'html5lib')
```

lxml 解析器可以解析 HTML 和 XML 文档，且速度快，容错强，推荐使用。

### 通用方法

`soup.prettify()`：以标准的缩进格式输出

> 这一步不是由prettify()方法完成的，而是在创建soup对象时就完成。

### 节点方法

Beautfiful Soup 将复杂 HTML 文档转换成一个复杂的树形结构，每个节点都是 Python 对象，所有对象可以归纳为4种：
- **tag**
- **NavigableString**
- **BeautifulSoup**
- **Comment**

#### 获取节点信息

`.name`：获取节点名称（如：div，span，li，a等），返回字符串对象

`.attrs`：获取节点属性（如：class，id，href等），返回字典对象

`.text`：获取节点元素下所有节点包含的文本内容，返回字符串对象

`.string`：获取节点元素下的文本内容，返回字符串对象

`.strings`：获取该节点及其所有子节点的文字，返回生成器对象

`.stripped_strings`：获取该节点及其所有子节点的文字并去除空行，返回生成器对象

#### 按关系查找节点

`.contents`：获取节点所有的子节点，只取一层，返回列表对象

> 如果节点当中有换行符，会被当做是 NavigableString 类型节点而作为一个子节点

`.children`：获取节点所有的子节点，只取一层，返回生成器对象

`.parent`：获取节点的直接父节点。
`.parents`：递归获取节点的所有祖先节点，返回生成器对象

`previous_sibling`：获取节点的前一个兄弟元素
`previous_siblings`：获取节点前面的兄弟元素列表，返回生成器对象

`next_sibling`：获取节点的后一个兄弟元素
`next_siblings`：获取节点后面的兄弟元素列表，返回生成器对象

`previous_element`：获取节点的前一个元素
`next_element`：获取节点的后一个元素

### 过滤器

 `find_all(name ,attrs ,string, limit, recursive, **kwargs )`：返回多个元素组成的列表
- `name`：节点名称（传入字符串类型）
- `attrs`：节点属性（传入字典类型）
- `string`：要查找的元素的文本内容
- `limit`：限制返回结果的数量
- `recursive`：是否递归查找，默认为True
- `**kwargs`：关键字参数形式的属性查找，例如 id='my-id'

`find(name ,attrs ,recursive ,string, **kwargs )`：返回单个元素
- `name`：节点名称（传入字符串类型）
- `attrs`：节点属性（传入字典类型）
- `string`：要查找的元素的文本内容
- `recursive`：是否递归查找，默认为True
- `**kwargs`：关键字参数形式的属性查找，例如 id='my-id'

> find的本质是find_all + limit=1

其余过滤方法：
- find_parents()：返回所有祖先节点
- find_parent()：返回直接父节点
- find_next_siblings()：返回后面所有的兄弟节点
- find_next_sibling()：返回后面的第一个兄弟节点
- find_previous_siblings()：返回前面所有的兄弟节点
- find_previous_sibling()：返回前面第一个兄弟节点
- find_all_next()：返回节点后所有符合条件的节点
- find_next()：返回节点后第一个符合条件的节点
- find_all_previous()：返回节点前所有符合条件的节点
- find_previous()：返回节点前所有符合条件的节点

> 如果没有合适过滤器，可以自定义一个方法，方法只接受一个元素参数，如果这个方法返回True表示当前元素匹配被找到。

> find_all() 几乎是 BeautifulSoup 中最常用的搜索方法。BeautifulSoup 定义了它的简写方法，下面两行代码是等价的:
> `soup.find_all('b')`
> `soup('b')`

### 示例

查找所有的 `<b>` 标签

```python
soup.find_all('b')
```

找出所有以b开头的标签

```python
soup.find_all(re.compile("^b"))
```

找出所有 `<a>` 标签和 `<b>` 标签

```python
soup.find_all(["a", "b"])
```

找出所有包含 class 属性但不包含 id 属性的标签

```python
def has_class_but_no_id(tag):
    return tag.has_attr('class') and not tag.has_attr('id')

print(soup.find_all(has_class_but_no_id))
```

### CSS选择器

```python
from bs4 import BeautifulSoup
html = """
<html>
<head><title>标题</title></head>
<body>
 <p class="title" name="dromouse"><b>标题</b></p>
 <div name="divlink">
  <p>
   <a href="http://example.com/1" class="sister" id="link1">链接1</a>
   <a href="http://example.com/2" class="sister" id="link2">链接2</a>
   <a href="http://example.com/3" class="sister" id="link3">链接3</a>
  </p>
 </div>
 <p></p>
 <div name='dv2'></div>
</body>
</html>
"""
soup = BeautifulSoup(html, 'lxml')
# 通过tag查找
print(soup.select('title'))             # [<title>标题</title>]

# 通过tag逐层查找
print(soup.select("html head title"))   # [<title>标题</title>]

# 通过class查找
print(soup.select('.sister'))
# [<a class="sister" href="http://example.com/1" id="link1">链接1</a>,
# <a class="sister" href="http://example.com/2" id="link2">链接2</a>,
# <a class="sister" href="http://example.com/3" id="link3">链接3</a>]

# 通过id查找
print(soup.select('#link1, #link2'))
# [<a class="sister" href="http://example.com/1" id="link1">链接1</a>,
# <a class="sister" href="http://example.com/2" id="link2">链接2</a>]

# 组合查找
print(soup.select('p #link1'))　　　　# [<a class="sister" href="http://example.com/1" id="link1">链接1</a>]

# 查找直接子标签
print(soup.select("head > title"))　 # [<title>标题</title>]
print(soup.select("p > #link1"))　　 # [<a class="sister" href="http://example.com/1" id="link1">链接1</a>]

print(soup.select("p > a:nth-of-type(2)"))　　# [<a class="sister" href="http://example.com/2" id="link2">链接2</a>]
# nth-of-type 是CSS选择器

# 查找兄弟节点（向后查找）
print(soup.select("#link1 ~ .sister"))
# [<a class="sister" href="http://example.com/2" id="link2">链接2</a>,
# <a class="sister" href="http://example.com/3" id="link3">链接3</a>]

print(soup.select("#link1 + .sister"))
# [<a class="sister" href="http://example.com/2" id="link2">链接2</a>]

# 通过属性查找
print(soup.select('a[href="http://example.com/1"]'))

# ^ 以XX开头
print(soup.select('a[href^="http://example.com/"]'))

# * 包含
print(soup.select('a[href*=".com/"]'))

# 查找包含指定属性的标签
print(soup.select('[name]'))

# 查找第一个元素
print(soup.select_one(".sister"))
```

### xpath

符号释义
- `/`：从根节点选取
- `//`：从当前节点开始选取
- `.`：选取当前节点
- `..`：选取当前节点的父节点
- `@`：选取属性
- `*`：匹配任何元素节点
- `@*`：匹配任何属性节点
- `node()`匹配任何类型的节点

举例
- `a[@herf]`：提取有herf属性的a标签
- `a[@class="123"]`：提取class属性值为123的a标签
- `a[@class="123" and @herf="hhhh"]`：添加多个属性

```python
print(title_object.xpath('text()'))  //获取标签内文本信息

print(title_object.xpath('@href'))//获取标签内href属性
```

## 资料

官方文档：[Beautiful Soup 4.4.0 文档](https://beautifulsoup.readthedocs.io/zh-cn/v4.4.0/)
