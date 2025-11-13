# Odoo字段约束

Odoo 提供了两种设置自动验证不变式的方法： Python constraints 和 SQL constraints 。

## SQL constraints

SQL 约束通过模型属性 **_sql_constraints** 进行定义 。该属性被分配给一个包含字符串(name, sql_definition, message) 的三元组列表，name是有效的SQL约束名称，sql_definition是table_constraint表达式，message是错误信息。

### 案例

```python
 _sql_constraints = [
     ('tag_name_unique', 'unique(name)', '标签名必须是唯一的'),
     ('tag_name_check', 'check(length(name)<20)', '标签名的长度必须小于20'),
     ('tag_sequence_check', 'check(sequence > 0 and sequence < 1000)', '排序的值必须大于0小于1000'),
 ]
```

## Python constraints

Python 约束被定义为⼀个用 **constrains()** 装饰的方法，并在记录集上调用。装饰器指定了约束所涉及的字段。当这些字段中的任何⼀个被修改时，约束将被自动评估。如果方法的不变式未满足，则会引发异常

### 案例

```python
@api.constrains('discount')
def _check_discount(self):
    # 遍历所有正在被操作的记录
    for record in self:
        if record.discount < 0 or record.discount > 100:
        raise ValidationError(_("折扣必须在 0 到 100 之间！"))
```

> SQL 约束通常比 Python 约束更有效。在性能⽅⾯，SQL 约束⽐ Python 约束更有优势。
