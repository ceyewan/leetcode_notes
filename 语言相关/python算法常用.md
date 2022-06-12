### Lambda表达式

Lambda 关键字用于小巧的匿名函数。`lambda a, b: a + b` 函数返回两个参数的和。Lambda 函数可用于任何需要函数对象的地方。在语法上，匿名函数只能是单个表达式。在语义上，它只是常规函数定义的语法糖。与嵌套函数定义一样，lambda 函数可以引用包含作用域中的变量：

```bash
>>> def make_incrementor(n):
...     return lambda x: x + n
...
>>> f = make_incrementor(42)
>>> f(0)
42
>>> f(1)
43

翻译一下就是: f = lambda x: x + 42
```

上例用 lambda 表达式返回函数。还可以把匿名函数用作传递的实参：

```bath
>>> pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
>>> pairs.sort(key=lambda pair: pair[1])
>>> pairs
[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
```

### 列表 list

```
list.append(x)
在列表末尾添加一个元素，相当于 a[len(a):] = [x] 。

list.extend(iterable)
用可迭代对象的元素扩展列表。相当于 a[len(a):] = iterable 。

list.insert(i, x)
在指定位置插入元素。第一个参数是插入元素的索引，因此，a.insert(0, x) 在列表开头插入元素， a.insert(len(a), x) 等同于 a.append(x) 。

list.remove(x)
从列表中删除第一个值为 x 的元素。未找到指定元素时，触发 ValueError 异常。

list.pop([i])
删除列表中指定位置的元素，并返回被删除的元素。未指定位置时，a.pop() 删除并返回列表的最后一个元素。（方法签名中 i 两边的方括号表示该参数是可选的，不是要求输入方括号。这种表示法常见于 Python 参考库）。

list.clear()
删除列表里的所有元素，相当于 del a[:] 。

list.index(x[, start[, end]])
返回列表中第一个值为 x 的元素的零基索引。未找到指定元素时，触发 ValueError 异常。

可选参数 start 和 end 是切片符号，用于将搜索限制为列表的特定子序列。返回的索引是相对于整个序列的开始计算的，而不是 start 参数。

list.count(x)
返回列表中元素 x 出现的次数。

list.sort(*, key=None, reverse=False)
就地排序列表中的元素（要了解自定义排序参数，详见 sorted()）。

list.reverse()
翻转列表中的元素。

list.copy()
返回列表的浅拷贝。相当于 a[:] 。
```

