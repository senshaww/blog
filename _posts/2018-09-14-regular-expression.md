---
layout: post
title: 正则表达式的基本使用
toc: true
cover: 
tags: ['正则表达式','python']
---

<!-- more -->
## 常见匹配模式

|模式| 描述|
| :- | :- |
| \w | 匹配字母数字及下划线 |
| \W | 匹配非字母数字下划线 |
| \s | 匹配任意空白字符，等价于 [\t\n\r\f]. |
| \S | 匹配任意非空字符 |
| \d | 匹配任意数字，等价于 [0-9] |
| \D | 匹配任意非数字 |
| \A | 匹配字符串开始 |
| \Z | 匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串 |
| \z | 匹配字符串结束 |
| \G | 匹配最后匹配完成的位置 |
| \n | 匹配一个换行符 |
| \t | 匹配一个制表符 |
| ^	| 匹配字符串的开头 |
| $	| 匹配字符串的末尾。|
| .	| 匹配任意字符，除了换行符\n，当re.DOTALL（re.S）标记被指定时，则可以匹配包括换行符的任意字符。也可以用"(.&#124;\n)"|
| [...]	| 用来表示一组字符,单独列出：[amk] 匹配 'a'，'m'或'k' |
| [^...] | 不在[]中的字符：[^abc] 匹配除了a,b,c之外的字符。|
| *	| 匹配0个或多个的表达式。|
| +	| 匹配1个或多个的表达式。|
| ?	| 匹配0个或1个由前面的正则表达式定义的片段，或指明一个非贪婪限定符|
| {n}| 精确匹配n个前面表达式。|
| {n, m} | 匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式|
| a&#124;b | 匹配a或b |
| ( ) | 匹配括号内的表达式，也表示一个组 |  


## re.match
re.match 尝试从字符串的**起始位置**匹配一个模式，如果**不是起始位置**匹配成功的话，match()就返回**none**。  
`re.match(pattern, string, flags=0)`

### 最常规的匹配
```python
import re

content = 'Hello 123 4567 World_This is a Regex Demo'
print(len(content))
result = re.match('^Hello\s\d\d\d\s\d{4}\s\w{10}.*Demo$', content)
print(result)
print(result.group())#匹配的字符，等于group(0)
print(result.span())
```
```
out：
<_sre.SRE_Match object; span=(0, 41), match='Hello 123 4567 World_This is a Regex Demo'>
Hello 123 4567 World_This is a Regex Demo
(0, 41)
```

### 泛匹配
```python
import re

content = 'Hello 123 4567 World_This is a Regex Demo'
result = re.match('^Hello.*Demo$', content)
print(result)
print(result.group())
print(result.span())
```
```
out:
<_sre.SRE_Match object; span=(0, 41), match='Hello 123 4567 World_This is a Regex Demo'>
Hello 123 4567 World_This is a Regex Demo
(0, 41)
```

### 匹配目标
```python
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('(^Hello\s(\d+)\sWorld.*Demo$)', content)
print(result)
print(result.group(2)) #从左看左括号
print(result.span())
```
```
out:
<_sre.SRE_Match object; span=(0, 40), match='Hello 1234567 World_This is a Regex Demo'>
1234567
(0, 40)
```

### 贪婪匹配
```python
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^He.*(\d+).*Demo$', content)
print(result)
print(result.group(1))
```
```
out:
<_sre.SRE_Match object; span=(0, 40), match='Hello 1234567 World_This is a Regex Demo'>
7
```

### 非贪婪匹配
```python
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^He.*?(\d+).*Demo$', content) #.*被贪婪匹配了
print(result)
print(result.group(1))
```
```
out:
<_sre.SRE_Match object; span=(0, 40), match='Hello 1234567 World_This is a Regex Demo'>
1234567
```


### 匹配模式
```python
import re

content = '''Hello 1234567 World_This
is a Regex Demo
'''
result = re.match('^He.*?(\d+).*?Demo$', content, re.S) #用re.DOTALL来标记.能匹配\n
print(result.group(1))
print(type(re.S))
```
```
out:
1234567
<enum 'RegexFlag'>
```


### 转义
正则表示式中有特殊含义的字符之前要加\转义！
```python
import re

content = 'price is $5.00'
result = re.match('price is $5.00', content)
print(result)
```
```
out:
None
```
---

```python
import re

content = 'price is $5.00'
result = re.match('price is \$5\.00', content)
print(result)
```
```
out:
<_sre.SRE_Match object; span=(0, 14), match='price is $5.00'>
```


**总结：尽量使用泛匹配、使用括号得到匹配目标、尽量使用非贪婪模式、有换行符就用re.S**

## re.search
re.search 扫描整个字符串并返回第一个成功的匹配。

```python
import re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
result = re.match('Hello.*?(\d+).*?Demo', content)
print(result)
```
```
out:
None
```

---
```
import re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
result = re.search('Hello.*?(\d+).*?Demo', content)
print(result)
print(result.group(1))
```
```
out:
<_sre.SRE_Match object; span=(13, 53), match='Hello 1234567 World_This is a Regex Demo'>
1234567
```

**总结：为匹配方便，能用search就不用match**

### 匹配演练
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君"><i class="fa fa-user"></i>但愿人长久</a>
        </li>
    </ul>
</div>'''
result = re.search('<li.*?active.*?singer="(.*?)">(.*?)</a>', html, re.S)
if result:
    print(result.group(1), result.group(2))
```
```
out:
齐秦 往事随风
```

---
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>'''
result = re.search('<li.*?singer="(.*?)">(.*?)</a>', html, re.S)
if result:
    print(result.group(1), result.group(2))
```
```
out:
任贤齐 沧海一声笑
```
---
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>'''
result = re.search('<li.*?singer="(.*?)">(.*?)</a>', html)
if result:
    print(result.group(1), result.group(2))
```
```
out:
<_sre.SRE_Match object; span=(389, 448), match='<li data-view="6"><a href="/4.mp3" singer="beyond>
beyond 光辉岁月
```
## re.findall
搜索字符串，以列表形式返回全部能匹配的子串。  
**返回的是括号所匹配到的结果**
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>'''
results = re.findall('<li.*?href="(.*?)".*?singer="(.*?)">(.*?)</a>', html, re.S)
print(results)
print(type(results))
for result in results:
    print(result)
    print(result[0], result[1], result[2])
```
```
out:
[('/2.mp3', '任贤齐', '沧海一声笑'), ('/3.mp3', '齐秦', '往事随风'), ('/4.mp3', 'beyond', '光辉岁月'), ('/5.mp3', '陈慧琳', '记事本'), ('/6.mp3', '邓丽君', '但愿人长久')]
<class 'list'>
('/2.mp3', '任贤齐', '沧海一声笑')
/2.mp3 任贤齐 沧海一声笑
('/3.mp3', '齐秦', '往事随风')
/3.mp3 齐秦 往事随风
('/4.mp3', 'beyond', '光辉岁月')
/4.mp3 beyond 光辉岁月
('/5.mp3', '陈慧琳', '记事本')
/5.mp3 陈慧琳 记事本
('/6.mp3', '邓丽君', '但愿人长久')
/6.mp3 邓丽君 但愿人长久
```
**注意：\w匹配的是能组成单词的字符，但在python3 中re默认支持的是unicode字符集，当然也支持汉字。只要加入re.A就可以解决这样问题**

---
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>
results = re.findall('<li.*?>\s*?(<a.*?>)?(\w+)(</a>)?\s*?</li>', html, re.S)  
print(results)
for result in results:
    print(result[1])
```
```
out:
[('', '一路上有你', ''), ('<a href="/2.mp3" singer="任贤齐">', '沧海一声笑', '</a>'), ('<a href="/3.mp3" singer="齐秦">', '往事随风', '</a>'), ('<a href="/4.mp3" singer="beyond">', '光辉岁月', '</a>'), ('<a href="/5.mp3" singer="陈慧琳">', '记事本', '</a>'), ('<a href="/6.mp3" singer="邓丽君">', '但愿人长久', '</a>')]
一路上有你
沧海一声笑
往事随风
光辉岁月
记事本
但愿人长久
```

## re.sub
替换字符串中每一个匹配的子串后返回替换后的字符串。
```python
import re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
content = re.sub('\d+', '', content)
print(content)
```
```
out:
Extra stings Hello  World_This is a Regex Demo Extra stings
```
---

```python
import re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
content = re.sub('\d+', 'Replacement', content)
print(content)
```
```
out:
Extra stings Hello Replacement World_This is a Regex Demo Extra stings
```
---


```python
import re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
content = re.sub('(\d+)', r'\1 8910', content)#\1表示前边匹配到的第一个括号
print(content)
print("\8")
```
```
out:
Extra stings Hello 1234567 8910 World_This is a Regex Demo Extra stings
\8
```
**注意：原来\1到\7都会被转义！！**
---
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>'''
html = re.sub('<a.*?>|</a>', '', html)
print(html)
results = re.findall('<li.*?>(.*?)</li>', html, re.S)
print(results)
for result in results:
    print(result.strip())
```
```
out:
<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            沧海一声笑
        </li>
        <li data-view="4" class="active">
            往事随风
        </li>
        <li data-view="6">光辉岁月</li>
        <li data-view="5">记事本</li>
        <li data-view="5">
            但愿人长久
        </li>
    </ul>
</div>
['一路上有你', '\n            沧海一声笑\n        ', '\n            往事随风\n        ', '光辉岁月', '记事本', '\n            但愿人长久\n        ']
一路上有你
沧海一声笑
往事随风
光辉岁月
记事本
但愿人长久
```


## re.compile
将一个正则表达式串编译成正则对象，以便于复用该匹配模式

```python
import re

content = '''Hello 1234567 World_This
is a Regex Demo'''
pattern = re.compile('Hello.*Demo', re.S)
result = re.match(pattern, content)
#result = re.match('Hello.*Demo', content, re.S)
print(result)
```
```
out:
<_sre.SRE_Match object; span=(0, 40), match='Hello 1234567 World_This\nis a Regex Demo'>
```

## 实战练习
爬取豆瓣图书  
发现：findall每次匹配的顺序不一样

```python
import requests
import re
content = requests.get('https://book.douban.com/').text
#pattern = re.compile('<li.*?cover.*?href="(.*?)".*?title="(.*?)".*?more-meta.*?author">(.*?)</span>.*?year">(.*?)</span>.*?</li>', re.S)
pattern = re.compile(r'<div class="info">.*?class="title".*?href="(.*?)"\s*title="(.*?)".*?more-meta.*?"author">\s*(.*?)\s*</span>.*?"publisher">\s*(.*?)\s*</span>.*?</div>', re.S)
#pattern = re.compile(r'<li.*?"info">.*?href="(.*?)"\s+title="(.*?)".*?more-meta.*?"author">\s+(.*?)\s+</span>.*?"publisher">\s+(.*?)\s+</span>.*?</li>',re.S)
results = re.findall(pattern, content)
i = 1
for result in results:
    href,title, author, publisher = result
    print(href,title,author,publisher,i)
    i=i+1
    
```
```
out:
https://book.douban.com/subject/30258976/?icn=index-editionrecommend 异见时刻 ［美］伊琳·卡蒙（Irin Carmon）莎娜·卡尼兹尼克（Shana Knizhnik） 湖南文艺出版社 1
https://book.douban.com/subject/27138720/?icn=index-editionrecommend 独抒己见 [美]纳博科夫 上海译文出版社 2
https://book.douban.com/subject/30310334/?icn=index-editionrecommend 无条件增长 [中]李践&nbsp;/&nbsp;[中]黄强 中信出版集团 3
https://book.douban.com/subject/30318261/?icn=index-editionrecommend 自控力：实操篇 （美）凯利·麦格尼格尔 黑天鹅图书·北京联合出版公司 4
https://book.douban.com/subject/30323979/?icn=index-editionrecommend 寿命图鉴 [日] 伊吕波株式会社 著&nbsp;/&nbsp;[日] 山口香绪里 绘 新世纪出版社 5
https://book.douban.com/subject/30257748/?icn=index-latestbook-subject 方岛 金宇澄 上海人民出版社 6
https://book.douban.com/subject/30240081/?icn=index-latestbook-subject 印度佛教史 [日] 平川彰 后浪丨北京联合出版公司 7
https://book.douban.com/subject/27605279/?icn=index-latestbook-subject 花街往事 路内 人民文学出版社 8
https://book.douban.com/subject/27621510/?icn=index-latestbook-subject 海 [德] 弗兰克·施茨廷 后浪丨四川人民出版社 9
https://book.douban.com/subject/30299549/?icn=index-latestbook-subject 我们这一代 李昆武 编绘 后浪丨湖南美术出版社 10
https://book.douban.com/subject/30254388/?icn=index-latestbook-subject 两个世界的战争 [美]安东尼·帕戈登(Anthony Pagden) 民主与建设出版社 11
https://book.douban.com/subject/30276928/?icn=index-latestbook-subject 亲爱的陌生人 [法] 卡米耶·茹尔迪 北京联合出版公司 12
https://book.douban.com/subject/26952667/?icn=index-latestbook-subject 隐剑孤影抄 [日] 藤泽周平 译林出版社 13
https://book.douban.com/subject/30237881/?icn=index-latestbook-subject 独自迈向生命的尽头 [奥]让·埃默里 三辉图书/鹭江出版社 14
https://book.douban.com/subject/30217938/?icn=index-latestbook-subject 罐头厂街 [美]约翰·斯坦贝克 人民文学出版社 15
https://book.douban.com/subject/30240074/?icn=index-latestbook-subject 我每天只工作3小时 [日]押井守 后浪丨四川人民出版社 16
https://book.douban.com/subject/30270979/?icn=index-latestbook-subject 斜眼狗 [法] 艾蒂安·达沃多 后浪丨北京联合出版公司 17
https://book.douban.com/subject/27911231/?icn=index-latestbook-subject 万物并作 [美] 濮德培 (Peter C. Perdue) 生活·读书·新知三联书店 18
https://book.douban.com/subject/30231603/?icn=index-latestbook-subject 火鸟 [日]手冢治虫 后浪丨北京联合出版公司 19
https://book.douban.com/subject/30289362/?icn=index-latestbook-subject 明年更年轻：运动赋能篇 [美] 克里斯·克劳利&nbsp;/&nbsp;亨利·洛奇 后浪丨北京联合出版公司 20
https://book.douban.com/subject/30262577/?icn=index-latestbook-subject 天使之触 [英] 乔纳森·莫里斯 新星出版社 21
https://book.douban.com/subject/30203779/?icn=index-latestbook-subject 空王冠 [英]丹·琼斯(Dan Jones) 社会科学文献出版社 22
https://book.douban.com/subject/27127568/?icn=index-latestbook-subject 春天 阿乙 四川文艺出版社 23
https://book.douban.com/subject/27197033/?icn=index-latestbook-subject 谁此时孤独 [奥]里尔克 华东师范大学出版社 24
https://book.douban.com/subject/30247619/?icn=index-latestbook-subject 坚果壳 [英] 伊恩·麦克尤恩 上海译文出版社 25
https://book.douban.com/subject/30186003/?icn=index-latestbook-subject 左道 [美]万志英（Richard von Glahn） 社会科学文献出版社 26
https://book.douban.com/subject/30281971/?icn=index-latestbook-subject 推理作家的信条 王稼骏 新星出版社 27
https://book.douban.com/subject/30276254/?icn=index-latestbook-subject 正午6 正午故事 台海出版社 28
https://book.douban.com/subject/30277786/?icn=index-latestbook-subject 软刺 [美] 艾米丽•福里德伦德 四川文艺出版社 29
https://book.douban.com/subject/30289118/?icn=index-latestbook-subject 未完的明治维新 [日]坂野润治 社会科学文献出版社 30
https://book.douban.com/subject/30299996/?icn=index-latestbook-subject 水獭先生的新邻居 李星明 连环画出版社 31
https://book.douban.com/subject/30187217/?icn=index-latestbook-subject 宋徽宗 [美] 伊沛霞 理想国 | 广西师范大学出版社 32
https://book.douban.com/subject/30237822/?icn=index-latestbook-subject 大问题 [美] 安德斯·尼尔森 后浪丨北京联合出版公司 33
https://book.douban.com/subject/30248298/?icn=index-latestbook-subject 四君主 [英] 约翰·朱利叶斯·诺威奇 民主与建设出版社 34
https://book.douban.com/subject/30243868/?icn=index-latestbook-subject 犹太警察工会 [美] 迈克尔·夏邦 中信出版社 35
https://book.douban.com/subject/30256364/?icn=index-latestbook-subject 精准表达 [日] 高田贵久 后浪丨江西人民出版社 36
https://book.douban.com/subject/27190142/?icn=index-latestbook-subject 黑地之绘 [日]松本清张 人民文学出版社 37
https://book.douban.com/subject/30300327/?icn=index-latestbook-subject 料理图鉴 [日]越智登代子 著 / 平野惠理子 绘 后浪 丨 湖南美术出版社 38
https://book.douban.com/subject/30278158/?icn=index-latestbook-subject 寻找邓巴 [英] 爱德华·圣奥宾 未读·文艺家·北京联合出版公司 39
https://book.douban.com/subject/30266273/?icn=index-latestbook-subject 写作这门手艺 [美] 约翰·麦克菲 浦睿文化·湖南文艺出版社 40
```
## END





