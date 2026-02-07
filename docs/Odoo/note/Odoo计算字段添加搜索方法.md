# Odoo 计算字段添加搜索方法

> 在Odoo中不存储数据的字段无法在search视图搜索，也不能通过env[''].search([])方法搜索
>
> 在字段添加搜索方法实现搜索

```python
class AccountMove(models.Model):
    _inherit = 'account.move'

    summary_from_first_line = fields.Char(string='摘要', compute='_compute_summary_from_first_line', search='_search_summary_from_first_line')

    @api.depends('line_ids')
    def _compute_summary_from_first_line(self):
        for rec in self:
            rec.summary_from_first_line = ''
            if rec.line_ids:
                sorted_lines = rec.line_ids.sorted(
                    key=lambda r: (r.sequence, r.id)
                )
                rec.summary_from_first_line = sorted_lines[0].name

    @api.model
    def _search_summary_from_first_line(self, operator, value):
        return [('line_ids.name', operator, value)]
```

```xml
<record id="view_account_move_filter_ext" model="ir.ui.view">
    <field name="name">会计凭证搜索视图 扩展</field>
    <field name="model">account.move</field>
    <field name="inherit_id" ref="account.view_account_move_filter"/>
    <field name="arch" type="xml">
         <xpath expr="//search" position="inside">
            <field name="summary_from_first_line" string="摘要"/>
        </xpath>
    </field>
</record>
```

