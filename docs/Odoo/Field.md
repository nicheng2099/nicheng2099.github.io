# 字段

在 Odoo 中，字段（Field）是模型（Model）的核心元素，不同的字段类型用于存储不同类型的数据，而字段属性则用于定义其行为、约束、展示方式等。以下是 Odoo 中常用的**字段类型**和**通用 / 专用属性**的详细说明：

### 一、常用字段类型

Odoo 的字段类型可分为**基础类型**（存储简单数据）和**关联类型**（处理模型间关系），具体如下：

#### 1. 基础字段类型（存储简单数据）

| 字段类型      | 说明                                                 | 示例场景               |
| --------- | -------------------------------------------------- | ------------------ |
| Char      | 字符串（短文本），默认长度 512 字符，可通过 size 限制前端输入长度。            | 名称、手机号、编码          |
| Text      | 长文本，无长度限制，适合存储描述、备注等大段文字。                          | 产品描述、订单备注          |
| Integer   | 整数（32 位），存储无小数的数值。                                 | 数量、年龄              |
| Float     | 浮点数，可通过 digits 定义精度（整数位 + 小数位）。                    | 价格、重量              |
| Monetary  | 货币类型，需配合 currency_field 指定货币字段（关联 res.currency）。   | 订单金额、成本            |
| Boolean   | 布尔值，存储 True/False（数据库中为 t/f）。                      | 是否激活、是否含税          |
| Date      | 日期类型（YYYY-MM-DD），前端显示为日期选择器。                       | 生日、订单日期            |
| Datetime  | 日期时间类型（YYYY-MM-DD HH:MM:SS），包含时间信息。                | 创建时间、最后更新时间        |
| Binary    | 二进制数据，用于存储文件（如图片、文档），需注意数据库存储压力。                   | 产品图片、附件            |
| Selection | 下拉选择框，值为预定义的选项列表（[(key, value), ...]）。             | 订单状态（草稿 / 确认 / 取消） |
| Html      | 富文本类型，支持 HTML 格式，前端显示为富文本编辑器。                      | 邮件内容、富文本说明         |
| Many2one  | 多对一关联（如 “订单” 关联 “客户”，多个订单对应一个客户）。                  | 订单→客户、员工→部门        |
| One2many  | 一对多关联（如 “客户” 关联 “订单”，一个客户对应多个订单），依赖 Many2one 反向定义。 | 客户→订单、部门→员工        |
| Many2many | 多对多关联（如 “产品” 关联 “标签”，一个产品可多个标签，一个标签可多个产品），通过中间表实现。 | 产品→标签、学生→课程        |

#### 2. 特殊字段类型

| 字段类型      | 说明                                                           |
| --------- | ------------------------------------------------------------ |
| Related   | 关联映射字段，通过已有关联字段引用目标模型的字段（值自动同步），本质是 Many2one/One2many 的快捷方式。 |
| Computed  | 计算字段，通过 compute 方法动态计算值，不直接存储（可通过 store=True 存储）。            |
| Reference | 动态关联字段，可关联任意模型的记录（如 “消息” 关联 “订单” 或 “客户”），值格式为 model_name,id。 |
| Property  | 企业级多公司字段，不同公司可设置不同值（需配合 res.company 使用）。                     |

### 二、字段的核心属性

字段属性分为**通用属性**（所有字段适用）和**专用属性**（特定类型字段专用），以下是常用属性：

#### 1. 通用属性（所有字段适用）

| 属性名       | 说明                                           | 示例                                                                    |
| --------- | -------------------------------------------- | --------------------------------------------------------------------- |
| string    | 字段在视图中显示的标签名称。                               | string="客户名称"                                                         |
| required  | 布尔值，是否为必填项（默认 False）。                        | required=True                                                         |
| readonly  | 布尔值，是否为只读（默认 False）。                         | readonly=True                                                         |
| default   | 默认值，可为固定值或返回值的函数（lambda 或方法）。                | default=0 或 default=lambda self: fields.Date.today()                  |
| help      | 帮助文本，鼠标悬停时显示提示。                              | help="请输入有效的邮箱地址"                                                     |
| groups    | 限制可见 / 编辑权限的用户组（多个组用逗号分隔）。                   | groups="base.group_system, my_module.group_manager"                   |
| states    | 根据记录状态（state 字段）动态设置 readonly/required。      | states={'draft': [('readonly', False)], 'done': [('readonly', True)]} |
| copy      | 复制记录时是否复制该字段（默认 True）。                       | copy=False（如订单编号不复制）                                                  |
| index     | 是否为字段创建数据库索引（加速查询，默认 False）。                 | index=True                                                            |
| tracking  | 整数，字段变更时是否记录跟踪日志（0 不跟踪，值越大优先级越高）。            | tracking=1                                                            |
| translate | 布尔值，是否支持多语言翻译（仅 Char/Text/Html 适用，默认 False）。 | translate=True                                                        |

#### 2. 专用属性（按字段类型分类）

##### （1）基础类型专用属性

| 字段类型           | 专用属性           | 说明                                         | 示例                                          |
| -------------- | -------------- | ------------------------------------------ | ------------------------------------------- |
| Char           | size           | 前端输入框长度限制（数据库仍存储 512 字符）。                  | size=50                                     |
| Char/Text      | placeholder    | 输入框占位提示文本（Odoo 12+ 支持）。                    | placeholder="请输入手机号"                        |
| Float/Monetary | digits         | 数值精度（元组 (整数位, 小数位)）。                       | digits=(10, 2)（10 位整数 + 2 位小数）              |
| Monetary       | currency_field | 关联的货币字段（如 'currency_id'，需关联 res.currency）。 | currency_field='currency_id'                |
| Selection      | selection      | 下拉选项列表（[(key, value), ...]），可为方法动态返回。      | selection=[('draft', '草稿'), ('done', '完成')] |
| Selection      | selection_add  | 继承模型时，在原有选项基础上添加新选项。                       | selection_add=[('cancel', '取消')]            |

##### （2）关联类型专用属性（Many2one/One2many/Many2many）

| 字段类型      | 专用属性            | 说明                                                          | 示例                                        |
| --------- | --------------- | ----------------------------------------------------------- | ----------------------------------------- |
| 所有关联类型    | comodel_name    | 关联的目标模型名称（One2many/Many2many 必选）。                           | comodel_name='res.partner'                |
| One2many  | inverse_name    | 目标模型中关联当前模型的 Many2one 字段名（必选）。                              | inverse_name='order_id'（订单行关联订单）          |
| Many2many | relation        | 自定义中间表名称（默认自动生成 model1_model2）。                             | relation='product_tag_rel'                |
| Many2many | column1/column2 | 中间表中当前模型 ID 和目标模型 ID 的字段名（默认 'id'）。                         | column1='product_id', column2='tag_id'    |
| 所有关联类型    | domain          | 过滤关联记录的条件列表（如只显示特定类型的记录）。                                   | domain=[('is_company', '=', True)]（只显示公司） |
| 所有关联类型    | context         | 传递给关联字段的上下文（影响默认值、视图过滤等）。                                   | context={'default_type': 'customer'}      |
| 所有关联类型    | auto_join       | 是否自动生成 SQL JOIN（优化查询性能，默认 False）。                           | auto_join=True                            |
| Many2one  | ondelete        | 主记录删除时的处理策略（'cascade' 级联删除、'set null' 设为空、'restrict' 禁止删除）。 | ondelete='cascade'                        |
| Many2one  | delegate        | 是否 “委托继承” 目标模型的字段（类似组合式继承，默认 False）。                        | delegate=True（当前模型可直接使用目标模型字段）            |

##### （3）计算字段专用属性（Computed）

| 属性名          | 说明                                | 示例                                 |
| ------------ | --------------------------------- | ---------------------------------- |
| compute      | 计算字段值的方法名（必选）。                    | compute='_compute_total'           |
| inverse      | 允许手动编辑计算字段的反向方法（可选）。              | inverse='_inverse_total'           |
| depends      | 计算依赖的字段（依赖字段变化时重新计算）。             | depends=['quantity', 'price_unit'] |
| store        | 是否将计算结果存储到数据库（默认 False，实时计算）。     | store=True                         |
| compute_sudo | 计算时是否以管理员权限执行（忽略当前用户权限，默认 False）。 | compute_sudo=True                  |
| search       | 定义计算字段的搜索逻辑（默认不可搜索）。              | search='_search_total'             |

### 三、总结

- **字段类型**决定了数据的存储格式和基础行为（如 Char 存字符串，Many2one 处理关联）。

- **字段属性**用于精细化控制（如 required 限制必填，ondelete 控制级联删除，compute 实现动态计算）。

根据业务需求选择合适的字段类型和属性组合，是构建 Odoo 模型的核心步骤。例如：订单金额用 Monetary 类型并指定货币精度，订单与客户的关联用 Many2one 并设置 ondelete='restrict' 避免误删客户导致订单孤立。
