# Odoo安排活动

> 通常在工作流审批时使用
> 
> 需继承: _inherit = ['mail.thread', 'mail.activity.mixin']模型

参考链接：[Module/odoo_send_notification · LC_Link/document - 码云 - 开源中国 (gitee.com)](https://gitee.com/LC_Link/document/tree/master/Module/odoo_send_notification)

## 在对应发送活动的按钮方法添加下列代码

```python
# 当前模型
res_model_id = self.sudo().env['ir.model'].search(
    [('model', '=', self._name)], limit=1).id
# 活动
Activity = self.env['mail.activity']
Activity.create({
    'activity_type_id': self.env.ref('mail.mail_activity_data_todo').id,
    'res_model_id': res_model_id,
    'res_id': self.id,
    'summary': f"用户{user.name}:\n{self.message}",
    'user_id': user.id,
    'date_deadline':self.deadline_date
})
# 返回提示
return {
    'type': 'ir.actions.client',
    'tag': 'display_notification',
    'params': {
        'message': f'已向 {user.name} 发送活动安排！',
        'type': 'success',
        'sticky': False
    }
}
```

## 在活动完成时的方法中加入

```python
self.ensure_one()
# 获取当前用户
current_user = self.env.user
# 找出对应的活动
my_activities = self.env['mail.activity'].search([
    ('res_model', '=', self._name),
    ('res_id', '=', self.id),
    ('user_id', '=', current_user.id),
    ('activity_type_id', '=', self.env.ref('mail.mail_activity_data_todo').id),
    ('state', '=', 'planned')
])

if not my_activities:
    raise UserError(_('您没有当前广播通知的未完成待办活动！'))
# 活动完成
my_activities.action_feedback(feedback='收到通知')
# 返回提示
return {
    'type': 'ir.actions.client',
    'tag': 'display_notification',
    'params': {
        'message': f'已成功完成您的{len(my_activities)}个待办活动！',
        'type': 'success',
        'sticky': False
    }
}
```

## 看板视图(案例)

```xml
<record id="view_broadcast_notification_kanban" model="ir.ui.view">
    <field name="name">broadcast.notification.kanban</field>
    <field name="model">broadcast.notification</field>
    <field name="arch" type="xml">
        <kanban class="o_kanban_mobile" create="false" delete="false" edit="false">
            <templates>
                <t t-name="kanban-box">
                    <div>
                        <h3>
                            <b>
                                广播通知
                            </b>
                        </h3>
                        <p>
                            <field name="title"/>
                        </p>
                        <div>
                            <span>发送人：
                                <field name="create_uid" widget="many2one_avatar_user"/>
                            </span>
                            <br/>
                            <span>创建日期：
                                <field name="create_date"/>
                            </span>
                            <br/>
                            <b>
                                <span>截止日期：
                                    <field name="deadline_date"/>
                                </span>
                            </b>
                        </div>
                        <footer style="display: flex; justify-content: space-between; align-items: center;padding:5px 10px;">
                            <button
                                    name="action_open_form"
                                    string="查看"
                                    type="object"
                                    class="btn btn-primary"
                            />
                            <button
                                    name="action_complete_activity"
                                    string="已阅"
                                    type="object"
                                    class="btn btn-primary"
                            />
                        </footer>
                    </div>
                </t>
            </templates>
        </kanban>
    </field>
</record>
```
