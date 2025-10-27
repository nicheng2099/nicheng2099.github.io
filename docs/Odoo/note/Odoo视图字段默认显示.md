# Odoo视图字段默认显示

## Odoo默认显示的标题字段

> 一般默认是name，但有时需要指定自定义字段做标题，在声明模块时采用_rec_name

```python
from odoo import models, fields

class CustomModel(models.Model):
    _name = 'custom.model'
    _description = 'Custom Model'
    _rec_name = 'reference_code'  # 使用reference_code作为默认显示字段

    name = fields.Char(string='Internal Name')
    reference_code = fields.Char(string='Reference Code', required=True)
    description = fields.Text(string='Description')
```

## 多对一默认显示字段

> 在多对一，比如类型字段，在视图页面显示的是name，通过设置options="{'display_name':'stone_size_remark'}"设置想要显示的字段

```python
custom_model_id = fields.Many2one(comodel_name='custom.model', string='custom_model')
custom_model_code = fields.Char(string='reference_code', related="custom_model_id .reference_code.name")
```

```xml
<field name="custom_model_id" optional="hide"
     options="{'display_name':'custom_model_code'}"></field>
<field name="custom_model_code " invisible="1"></field>
```
