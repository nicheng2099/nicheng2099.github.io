# Odoo excel字段

## 动态excel字段

1. 继承html.table.mixin模型

   ```python
   _inherit = 'html.table.mixin'
   ```

2. 重写_prepare_table_data方法

   ```python
   def _prepare_table_data(self):
   	title_format = 'font-weight: bold;'
       self.fill_cell(1, 1, '在第1行第1列插入字段', title_format)
   ```

3. 在视图引用table_html

   ```xml
   <group col="1">
       <label for="table_html" string="汇总"/>
       <div class="o_row">
           <field name="table_html" widget="html"></field>
       </div>
   </group>
   ```

## 存储excel字段

1. 继承html.table.mixin模型

   ```python
   _inherit = 'html.table.mixin'
   ```

2. 在方法中调用_set_table_html_store_header和_add_table_html_store_rows方法添加表头和数据

   ```python
   # 示例
   def add_sample_data(self):
        # 设置表头
        headers = ['编码', '名称', '价格']
        self.table_html_store = self._set_table_html_store_header(headers)
   
        # 添加数据
        data = [
            ['P001', '产品A', '100'],
            ['P002', '产品B', '200'],
        ]
   
        self._add_table_html_store_rows(data)
   ```

3. 在视图引用table_html_store

   ```xml
   <field name="table_html_store" widget="html" readonly="1"/>
   ```

## 源代码

```python
# -*- coding: utf-8 -*-
from odoo import models, fields
import re

DATA_ROW_STYLE = """
<style>
    .table-container {
        width: 100%;
    }
    .table-container table {
        width: 100%; 
        border-collapse: collapse; 
        font-size: 12px; 
        border: 1px solid #ddd;
    }
    .table-container table thead tr th {
        border: 1px solid #4CAF50; 
        background-color: #f5f5f5; 
        font-weight: bold; 
        position: sticky; 
        top: 0;
    }
    .table-container table thead tr th:first-child {
        width: 50px;
    }
    .table-container table tbody tr td {
        padding: 6px;
        border: 1px solid #ddd;
    }
    .table-container table tbody tr td:first-child {
        text-align: center;
    }
    .table-container table tbody tr td:not(:first-child) {
        text-align: left;
    }
</style>
"""

def _generate_table_html(table_data, rows, cols):
    html = '''
    <table border-collapse: collapse;style="width: 100%;margin: 0; padding: 10;">
        <tbody>
    '''
    for row in range(rows):
        html += f'<tr>'
        for col in range(cols):
            cell_key = f'{row}_{col}'
            cell_info = table_data.get(cell_key, {})
            cell_value = cell_info.get('data', '') if isinstance(cell_info, dict) else cell_info
            cell_style = cell_info.get('style', '') if isinstance(cell_info, dict) else ''
            style_attr = f' style="{cell_style}"' if cell_style else ''
            html += f'<td {style_attr}>{cell_value}</td>'
        html += '</tr>'
    html += '''
        </tbody>
    </table>
    '''

    return html


def _calculate_table_size(table_data):
    max_rows = 0
    max_cols = 0

    for key in table_data:
        if '_' in key:
            try:
                row, col = map(int, key.split('_'))
                max_rows = max(max_rows, row + 1)
                max_cols = max(max_cols, col + 1)
            except Exception as e:
                raise ValueError(f'表格数据传值错误：{str(e)}')

    return max_rows, max_cols


class HtmlTableMixin(models.AbstractModel):
    _name = 'html.table.mixin'
    _description = 'HTML Table Mixin'
    _abstract = True

    # 动态table
    table_html = fields.Html(string='Table HTML', compute='_compute_table_html')
    table_data = fields.Json(string='Table Data', compute='_prepare_table_data', help='表格数据，JSON格式')

    def _compute_table_html(self):
        for record in self:
            record.table_data = {}
            record._prepare_table_data()
            table_data = record.table_data or {}
            max_rows, max_cols = _calculate_table_size(table_data)
            max_rows = max(max_rows, 1)
            max_cols = max(max_cols, 1)
            record.table_html = _generate_table_html(table_data, max_rows, max_cols)

    def _prepare_table_data(self):
        """
        准备表格数据

        子类应重写此方法，在此方法内调用 fill_cell 填充数据
        表格 table_html 会自动根据填充的数据生成
        """
        self.fill_cell(0, 0, None, None)

    # 填入数据的方法
    def fill_cell(self, row, col, data, style=None):
        self.ensure_one()
        if row < 0 or col < 0:
            raise ValueError('表格数据的行和列必须大于零')
        table_data = self.table_data or {}
        table_data[f'{row}_{col}'] = {
            'data': data,
            'style': style
        }
        self.table_data = table_data

    # 存储table
    table_html_store = fields.Html(string='Table HTML Store', sanitize=False)
    
    def _set_table_html_store_header(self, column_names):
        """
            ['ID', '名称', '值']
        """
        if not column_names:
            self.table_html_store = ""
            return

        # 设置表头
        header_cells = (
            '<th>序号</th>' + "".join([f'<th>{col}</th>' for col in column_names])
        )
        # 定义table
        html = f"""
            {DATA_ROW_STYLE}
            <div class="table-container">
                <table>
                    <thead><tr>{header_cells}</tr></thead>
                    <tbody></tbody>
                </table>
            </div>
        """

        self.write({'table_html_store': html})

    def _add_table_html_store_row(self, row_data):
        """
            ['P001', '产品A', '100']
        """
        html_content = self.table_html_store or ""
        if not html_content or '<table' not in html_content:
            # 无表格则创建基本表头
            column_names = [f'列{i + 1}' for i in range(len(row_data))]
            self._set_table_html_store_header(column_names)
            html_content = self.table_html_store

        # 计算当前行数
        # 统计已有的<tr>数量
        row_count = html_content.count('<tr>') - 1  # 表头有1个tr，剩下的都是数据行
        serial_number = row_count + 1

        # 构建数据行
        data_cells = (
            f'<td>{serial_number}</td>'
            + "".join([f'<td>{cell or ""}</td>' for cell in row_data])
        )

        row_html = f'<tr>{data_cells}</tr>'

        # 插入到tbody
        if '</tbody>' in html_content:
            updated_html = re.sub(r'\s*</tbody>\s*', f'{row_html}</tbody>', html_content)
            self.write({'table_html_store': updated_html})

    def _add_table_html_store_rows(self, rows_data):
        """
            批量添加
        """
        for row_data in rows_data:
            self._add_table_html_store_row(row_data)

    def _clear_table_html_store(self):
        """
            清空表格
        """
        if not self.table_html_store:
            return

        # 清除tbody部分
        html = self.table_html_store
        if '<tbody>' in html and '</tbody>' in html:
            tbody_start = html.find('<tbody>')
            header_part = str(html[:tbody_start + 7])
            self.write({'table_html_store': header_part + '</tbody></table></div>'})

    # # 示例
    # def add_sample_data(self):
    #     # 设置表头
    #     headers = ['编码', '名称', '价格']
    #     self.table_html_store = self._set_table_html_store_header(headers)
    #
    #     # 添加数据
    #     data = [
    #         ['P001', '产品A', '100'],
    #         ['P002', '产品B', '200'],
    #     ]
    #
    #     self._add_table_html_store_rows(data)
```

