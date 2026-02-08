# Odoo动态域设置

使用expression拼接domain

参考代码

```python
from odoo.osv import expression

def get_production_domain(self):
    domain = [
        ('company_id', '=', self.company_id.id),
        ('state', 'in', [s.value for s in self.production_state_ids]),
        ('is_adjusted', '=', False),  # 只拿末关账的工单！
    ]
    if self.is_outsource:
        domain = expression.AND([domain, [('is_outsource', '=', self.is_outsource)]])
    if self.period_id:
        s = self.date_start
        t = self.date_stop
        start = datetime(year=s.year, month=s.month, day=s.day, hour=0, minute=0, second=0)
        stop = datetime(year=t.year, month=t.month, day=t.day, hour=23, minute=59, second=59)
        # 已完工
        dm1 = expression.AND([[('enter_warehouse_time', '<=', stop)], [('enter_warehouse_time', '>=', start)]])
        # 末完工
        dm2 = expression.AND([[('enter_warehouse_time', '=', False)], [('started_time', '<', stop)]])
        sdm3 = expression.OR([dm1, dm2])
        domain = expression.AND([domain, sdm3])
    if self.production_type_ids:
        d1 = [('mo_type', 'in', self.production_type_ids.mapped("value"))]
        d2 = [('mo_type', '=', False)]
        if self.include_empty_mo_type:
            domain = expression.AND([domain, expression.OR([d1, d2])])
        else:
            domain = expression.AND([domain, d1])
    else:
        if self.include_empty_mo_type:
            domain = expression.AND([domain, [('mo_type', '=', False)]])
    if self.production_site_ids:
        domain = expression.AND(
            [domain, [('bill_id.production_site', 'in', self.production_site_ids.mapped("value"))]])
    if self.order_source_ids:
        domain = expression.AND([domain, [('order_source', 'in', self.order_source_ids.mapped("value"))]])
    if self.business_type_ids:
        domain = expression.AND([domain, [('business_type', 'in', self.business_type_ids.ids)]])

    if self.mo_line_ids:
        domain = expression.AND([domain, [('production_line', 'in', self.mo_line_ids.ids)]])
    print("生产订单过滤条件：", domain)
    return domain
```

