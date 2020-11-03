# Lua 元表(Metatable)

## 如何将表转换元表

```lua
mytable = {}                          -- 普通表
mymetatable = {}                      -- 元表
setmetatable(mytable,mymetatable)     -- 把 mymetatable 设为 mytable 的元表
```

或

```lua
mytable = setmetatable({},{})
```

或

```lua
getmetatable(mytable)
```

## __index

`__index` 

当你通过键来访问 table 的时候，如果这个键没有值，那么Lua就会寻找该 table 的 metatable（假定有 metatable）中的`__index` 键。`__index`包含一个表格，Lua会在表格中查找相应的键。

```lua
local mtable = setmetatable({key1="vlaue1"}, {
    -- 注一定要两个下划线是__index
    __index = function (mytable, key)
        if key == 'key2' then
            return 'value2'
        end
    end
})
-- value2
print(mtable.key2)
```
等价于
```lua
mytable = setmetatable({key1 = "value1"}, { __index = { key2 = "metatablevalue" } })
print(mytable.key1,mytable.key2)
```

> 1. mtable 表赋值为 {key1 = "value1"}
> 2. mtable 设置了元表，元方法为 __index
> 3. 在mtable 表中查找 key1，如果找到，返回该元素，找不到则继续。
> 4. 在mtable 表中查找 key2，如果找到，返回 metatablevalue，找不到则继续。
> 5. 判断元表有没有`__index`方法，如果`__index`方法是一个函数，则调用该函数。
> 6. 元方法中查看是否传入 "key2" 键的参数（mtable.key2已设置），如果传入 "key2" 参数返回 "metatablevalue"，否则返回 mtable 对应的键值。

### 总结

1. 在表中查找，如果找到，返回该元素，找不到则继续
2. 判断该表是否有元表，如果没有元表，返回 nil，有元表则继续。
3. 判断元表有没有 `__index` 方法，如果 `__index` 方法为 nil，则返回 nil；如果`__index` 方法是一个表，则重复 1、2、3；如果 `__index` 方法是一个函数，则返回该函数的返回值。

## __newindex

`__newindex`  

- 设置之后可以 原 table 修改值但是新设置的值会调用 `__newindex` 方法设置

```lua
local mymetatable = {}
local mytable = setmetatable({key1 = "value1"}, { __newindex = mymetatable })

print(mytable.key1)

mytable.newkey = "value2"
print(mytable.newkey, mymetatable.newkey)

mytable.key1 = "value1"
print(mytable.key1, mymetatable.key1)
```
```text
value1
nil    新值2
新值1    nil
```
```text
设置了元方法 __newindex，在对新索引键（newkey）赋值时（mytable.newkey = "新值2"），会调用元方法，而不进行赋值。而如果对已存在的索引键（key1），则会进行赋值，而不调用元方法 __newindex
```

- 以下实例使用了 rawset 函数来更新表：

```lua
mytable = setmetatable({key1 = "value1"}, {
  __newindex = function(mytable, key, value)
                rawset(mytable, key, "\""..value.."\"")

  end
})

mytable.key1 = "new value"
mytable.key2 = 4

print(mytable.key1,mytable.key2)
```

```
new value    "4"
```

## 为表添加操作符

- `__add` 操作符

```lua
mytable = setmetatable({ 1, 2, 3 }, {
    __add = function(mytable, newtable)
      for i = 1, #newtable do
        table.insert(mytable,newtable[i])
      end
      return mytable
    end
  })
local secondtable = {4,5,6}
mytable = mytable + secondtable
for key, value in pairs(mytable) do
    print(value)
end
```

- 其余操作符方法

![](https://cdn.jsdelivr.net/gh/xiaou66/picture/image/1604402880695-1604402880691.png)

## __tostring

```lua
mytable = setmetatable({ 10, 20, 30 }, {
    __tostring = function(mytable)
      local s = ""
      for key, value in pairs(mytable) do
        s = s .. "key: " .. key .. " value: " .. value
      end
      return s
    end
  })
print(mytable)
```

```
key: 1 value: 10key: 2 value: 20key: 3 value: 30
```

## __call

```lua
function table_maxn(t)
    local mn = 0
    for k, v in pairs(t) do
        if mn < k then
            mn = k
        end
    end
    return mn
end

-- 定义元方法__call 计算两个table的和
mytable = setmetatable({10}, {
  __call = function(mytable, newtable)
        sum = 0
        for i = 1, table_maxn(mytable) do
                sum = sum + mytable[i]
        end
    for i = 1, table_maxn(newtable) do
                sum = sum + newtable[i]
        end
        return sum
  end
})
newtable = {10,20,30}
print(mytable(newtable))
```

```
70
```



