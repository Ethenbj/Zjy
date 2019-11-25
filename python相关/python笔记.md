# Python笔记

### 一、Python运算符

##### 1.Python中“<>”等价于“!=”

### 二、列表和元组

##### 1. 删除元素

```python
>>> x
[1, 2, 0]
>>> del x[1]
>>> x
[1, 0]	#del删除列表元素
```

##### 2. count用法

```python
>>> lst=['cloud','to','year','cloud','python']
>>> lst.count(‘cloud’)   #count方法统计某个元素在列表出现的次数
```

##### 3. extend用法

```python
>>> lst=['cloud','to','year','cloud','python']
>>> lst2=['c','java']
>>> lst.extend(lst2) 	#extend在列表中增加另一个序列中的多个值
>>> lst
['cloud', 'to', 'year', 'cloud', 'python', 'c', 'java']
>>> lst+lst2		#extend与+连接操作
['cloud', 'to', 'year', 'cloud', 'python', 'c', 'java', 'c', 'java']
>>> lst
['cloud', 'to', 'year', 'cloud', 'python', 'c', 'java']
```

##### 4. insert用法

```python
>>> lst=['cloud', 'to', 'year', 'cloud', 'python', 'c', 'java']
>>> lst.insert(3, ‘shell’)   #insert方法用于将对象插入到列表中
>>> lst
['cloud', 'to', 'year', 'shell', 'cloud', 'python', 'c', 'java'] 
```

##### 5.reverse用法

```python
>>> a = [5,6,4,2,7,8]
>>> a.reverse()
>>> a
[8, 7, 2, 4, 6, 5]			#输出以反方向形式输出
```

##### 6. sort用法

```python
>>> a = [5,6,4,2,7,8]
>>> a.sort()
>>> a
[2, 4, 5, 6, 7, 8]			#按顺序输出
```

##### 7. tuple转元组

```python
>>> tuple('hello')
('h', 'e', 'l', 'l', 'o')
```

# 字符串

### 一、python字符串大小写转换

##### 1. lower的用法

```python
>>> a = 'AaBbCc'
>>> print(a.lower())
aabbcc				#将其全部转为小写
#islower是判断是否都为小写
```

##### 2. upper的用法

```python
>>> a = 'AaBbCc'
>>> print(a.upper())
AABBCC				#将其全部转为大写
#isupper是判断是否都为大写
```

##### 3. capitalize的用法

```python
>>> a = 'aNB bjdJJ bBBa aaa BBB'
>>> print(a.capitalize())
Anb bjdjj bbba aaa bbb   #将整串字符串的第一个字母转为大写，其余全为小写
```

##### 4. title的用法

```python 
>>> a = 'aNB bjdJJ bBBa aaa BBB'
>>> print(a.title())
Anb Bjdjj Bbba Aaa Bbb   #将整串字符串中一空格隔开的每一小段字符串的首字母转为大写，其余全为小写
#istitle判断是否整串字符串中一空格隔开的每一小段字符串的首字母转为大写，其余全为小写
```

###二、

##### 1. replace的用法

```python 
>>> 'This is a test'.replace('is', 'eez')
'Theez eez a test'       #替换string中所有匹配
```

##### 2. split的用法【分割string，是join的逆方法】

```python
>>> '1+2+3+4+5'.split('+')
['1', '2', '3', '4', '5']
```

##### 3. strip 【去除两边的相同的元素】

```python
>>> a = 'B aNB bjdJJ bBBa aaa BBB'
>>> print(a.strip('B'))
 aNB bjdJJ bBBa aaa 
```

```python
>>> print(a.strip("a B"))
NB bjdJJ b				#指定需要去除的字符
```

# 条件与循环

### 一、for循环

##### 1. 三元表达式

```python 
>>> print([i for i in range(10)])
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]		#输出列表类型
```

##### 2. 斐波拉契数列

```python
>>> a = [0,1]
>>> for i in range(8):
	a.append(a[-1] + a[-2])
>>> a
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

##### 3. enumerate用法 

```python
>>>seq = ['one', 'two', 'three']
>>> for i, element in enumerate(seq):
...     print i, element
... 
0 one
1 two
2 three
```

###二、if表达式 

##### 1. 三元表达式

```python
>>> a,b=2,3
>>> print(a if a>b else b)
3
```

# 函数

### 一、函数的参数

##### 1. 形式参数与实际参数

函数在定义的时候参数为形式参数。函数调用的时候参数为实际参数。

for example:

```python
def computer(x,y,oper):
    result={
        "+":x+y,
        "-":x-y,
        "*":x*y,
        "/":x/y,
        }
    return result
#函数调用：
>>> computer(2,3,'+')

```

##### 2. 可变位置参数与可变关键字参数

*args：是一个列表，传入的参数会被放进列表里

**kwargs：是一个字典，传入的参数以键值对的形式存放到字典里

for example：

```python
def Jiafa(*args):
    sum = 0
    for i in args:
        sum+=i
    print(sum)
    
Jiafa(1,3,5)
Jiafa(2,4,6,8)
#运行之后
9
20
>>> 
```

```python
def Jiafa(**args):
    print(args)
    
Jiafa(a=1,b=3,c=5)
Jiafa(a=2,b=4,c=6,d=8)
#运行之后
{'a': 1, 'b': 3, 'c': 5}
{'a': 2, 'b': 4, 'c': 6, 'd': 8}
>>> 
```

# 字典

### 一、字典的常用方法

##### 1. copy的用法

```python
>>> a = {'name': 'ZJY', 'age': '18', 'ccc': ['a', 'b','c']}
>>> b = a.copy()
>>> b
{'name': 'ZJY', 'age': '18', 'ccc': ['a', 'b', 'c']}
```







