注意事项:
```python
import polars as pl

# 创建一个示例DataFrame
data = {
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'city': ['New York', 'London', 'Paris']
}
df = pl.DataFrame(data)

# 遍历DataFrame的行
for row in df.iter_rows():
    print(row)

#{'name': 'Alice', 'age': 25, 'city': 'New York'}
#{'name': 'Bob', 'age': 30, 'city': 'London'}
#{'name': 'Charlie', 'age': 35, 'city': 'Paris'}

```


