# 模块的约束
```Odoo提供了两种方式设置自动验证的不变量：Python的约束和SQL约束。
Python的约束被定义为一种装饰constrains()，能够被对象调用。装饰指定那些字段被约束调用，这样的约束能够自动评估哪一个已经被修改，如果不满足固定的条件，这个方法就会抛出异常```

from openerp.exceptions import ValidationError
```Python

@api.constrains('age')
def _check_something(self):
    for record in self:
        if record.age > 20:
            raise ValidationError("Your record is too old: %s" % record.age)
    # all records passed the test, don't return anything

```


>练习
添加Python约束添加一个约束,检查教练是不是他自己的会话的参与者


```通过模块attribute_sql_constraints定义SQL。后者被分配给一个三元组的字符串列表(name、sql_definition message),name是一个有效的SQL名称约束,如果sql_definition是一个table_constraint表达式,那么message就会现错误信息```


>练习
添加SQL约束通过PostgreSQL的帮助文档,添加以下约束:
1.检查课程描述和课程名称是不同的
2.使这门课的名字独一无二的

**openacademy/models.py**
```python
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sessions")

    _sql_constraints = [
        ('name_description_check',
         'CHECK(name != description)',
         "The title of the course should not be the description"),

        ('name_unique',
         'UNIQUE(name)',
         "The course title must be unique"),
    ]


class Session(models.Model):
    _name = 'openacademy.session'

```

>练习
练习6 -添加复制选项
因为我们添加了一个课程名称唯一性约束,不可能使用“复制”功能了(表格重复)。
重新实现自己的“复制”方法,该方法能够复制对象,原来的名字改为”copy of(原名)”。

**openacademy/models.py**
```python
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sessions")

    @api.multi
    def copy(self, default=None):
        default = dict(default or {})

        copied_count = self.search_count(
            [('name', '=like', u"Copy of {}%".format(self.name))])
        if not copied_count:
            new_name = u"Copy of {}".format(self.name)
        else:
            new_name = u"Copy of {} ({})".format(self.name, copied_count)

        default['name'] = new_name
        return super(Course, self).copy(default)

    _sql_constraints = [
        ('name_description_check',
         'CHECK(name != description)',
```
# 先进的视图


## 树视图
```树视图可以补充属性进一步定义他们的行为:decoration-{$name}
基于文本相应的记录属性，可以改变行的风格
Python值表达式。对于每一个记录,表达式用记录的属性作为的表达式的文本值,当这个值为true的时候，相应的样式应用到行中。其他的文本值是uid(id是当前用户的) current_date (当前日期是形式像yyyy-MM-dd的字符串){$name} 可以设为bf (font-weight: bold), it(font-style: italic),或任何引导背景颜色(danger,info, muted, primary, success or warning)。```
```Python
<tree string="Idea Categories" decoration-info="state=='draft'"
    decoration-danger="state=='trashed'">
    <field name="name"/>
    <field name="state"/>
</tree>
```


**可编辑的**
```"top"或"bottom"中的一个。使树视图可以在相应的位置进行编辑(而不是必须通过表格视图),值的位置在新的行。```



>练习
列表颜色
修改会话树视图为：会话持续少于5天的颜色为蓝色,持续超过15天的颜色为红色。
修改会话树视图:

**openacademy/views/openacademy.xml**
python
            <field name="name">session.tree</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <tree string="Session Tree" decoration-info="duration&lt;5" decoration-danger="duration&gt;15">
                    <field name="name"/>
                    <field name="course_id"/>
                    <field name="duration" invisible="1"/>
                    <field name="taken_seats" widget="progressbar"/>
                </tree>
            </field>

```

# 日历
```显示记录日历事件。他们的根元素是<calendar>，最常见的属性是: color
字段的名称用于色彩分割。颜色会自动分配到事件,但事件在同一颜色段(记录的@color字段值)将会赋相同的颜色值
date_start
记录的字段开始日期/时间的事件
date_stop(可选)
记录的字段持有的结束日期/时间的事件字段(定义标签为每个日历事件)```

```python
<calendar string="Ideas" date_start="invent_date" color="inventor_id">
    <field name="name"/>
</calendar>
```

>练习
日历视图
在会话模块下面添加一个日历视图使用户能够在相应的Open Academy观察到事件日程表视图添加到会话模型允许用户查看开放学院相关的事件。
1.添加一个end_date字段，从start_date开始计算 到duration
提示：逆函数使字段可写,并允许在日历视图中移动会议(通过拖拽)
2.将会话视图添加到会话模块中
3.然后添加日历视图到会话模块的行为中

**openacademy/models.py**
```python
# -*- coding: utf-8 -*-

from datetime import timedelta
from openerp import models, fields, api, exceptions

class Course(models.Model):
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    taken_seats = fields.Float(string="Taken seats", compute='_taken_seats')
    end_date = fields.Date(string="End Date", store=True,
        compute='_get_end_date', inverse='_set_end_date')

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
                },
            }

    @api.depends('start_date', 'duration')
    def _get_end_date(self):
        for r in self:
            if not (r.start_date and r.duration):
                r.end_date = r.start_date
                continue

            # Add duration to start_date, but: Monday + 5 days = Saturday, so
            # subtract one second to get on Friday instead
            start = fields.Datetime.from_string(r.start_date)
            duration = timedelta(days=r.duration, seconds=-1)
            r.end_date = start + duration

    def _set_end_date(self):
        for r in self:
            if not (r.start_date and r.end_date):
                continue

            # Compute the difference between dates, but: Friday - Monday = 4 days,
            # so add one day to get 5 days instead
            start_date = fields.Datetime.from_string(r.start_date)
            end_date = fields.Datetime.from_string(r.end_date)
            r.duration = (end_date - start_date).days + 1

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
```
```python
openacademy/views/openacademy.xml
            </field>
        </record>

        <!-- calendar view -->
        <record model="ir.ui.view" id="session_calendar_view">
            <field name="name">session.calendar</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <calendar string="Session Calendar" date_start="start_date"
                          date_stop="end_date"
                          color="instructor_id">
                    <field name="name"/>
                </calendar>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
```


## 搜索视图
```搜索视图<field>元素可以有一个@filter_domain覆盖生成的字段在给定的域中搜索给定的字段。Self代表用户输入的值。在下面的示例中,它是用来搜索name 字段和 description.字段
搜索视图也可以包含<filter>元素,作为预定义的搜索的切换。Filters必须有以下属性:
domain
将给定域添加到当前的搜索
context
添加一些上下文当前搜索;使用关键字group_by依据字段名称给当前的结果分组```

```python
<search string="Ideas">
    <field name="name"/>
    <field name="description" string="Name and description"
           filter_domain="['|', ('name', 'ilike', self), ('description', 'ilike', self)]"/>
    <field name="inventor_id"/>
    <field name="country_id" widget="selection"/>

    <filter name="my_ideas" string="My Ideas"
            domain="[('inventor_id', '=', uid)]"/>
    <group string="Group By">
        <filter name="group_by_inventor" string="Inventor"
                context="{'group_by': 'inventor_id'}"/>
    </group>
</search>
```


```在一个活动中使用非默认搜索视图,它应该被操作记录里面的thesearch_view_id字段连接
为了能够通过它自己的content字段来进行字段搜索，这个活动也可以设置默认值，通过给search_default_field_name赋值，能够初始化name字段。搜索过滤器必须有一个可选的@ name作为默认,这个默认值是布尔类型的(他们只能默认启用)。```


>练习
搜索视图
在搜索视图中为当前的用户添加一个按钮来过滤的课程。并让它默认选中。
添加一个由用户负责的按钮组课程。


**openacademy/views/openacademy.xml**
```python
                <search>
                    <field name="name"/>
                    <field name="description"/>
                    <filter name="my_courses" string="My Courses"
                            domain="[('responsible_id', '=', uid)]"/>
                    <group string="Group By">
                        <filter name="by_responsible" string="Responsible"
                                context="{'group_by': 'responsible_id'}"/>
                    </group>
                </search>
            </field>
        </record>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
            <field name="context" eval="{'search_default_my_courses': 1}"/>
            <field name="help" type="html">
                <p class="oe_view_nocontent_create">Create the first course
                </p>
```

## 甘特
水平条形图通常用于显示项目规划和改进,它们的根元素<gantt>。
```python
<gantt string="Ideas"
       date_start="invent_date"
       date_stop="date_finished"
       progress="progress"
       default_group_by="inventor_id" />

```

>练习
甘特图
添加一个甘特图并授予用户查看链接到Open Academy模块的会话事件安排的权限。会话应由讲师分组。
创建一个计算字段表达会话持续时间
添加甘特视图的定义,添加甘特视图到会话模块的操作中

**openacademy/models.py**
```python
    end_date = fields.Date(string="End Date", store=True,
        compute='_get_end_date', inverse='_set_end_date')

    hours = fields.Float(string="Duration in hours",
                         compute='_get_hours', inverse='_set_hours')

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
            end_date = fields.Datetime.from_string(r.end_date)
            r.duration = (end_date - start_date).days + 1

    @api.depends('duration')
    def _get_hours(self):
        for r in self:
            r.hours = r.duration * 24

    def _set_hours(self):
        for r in self:
            r.duration = r.hours / 24

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
```
**openacademy/views/openacademy.xml**
```python
            </field>
        </record>

        <record model="ir.ui.view" id="session_gantt_view">
            <field name="name">session.gantt</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <gantt string="Session Gantt" color="course_id"
                       date_start="start_date" date_delay="hours"
                       default_group_by='instructor_id'>
                    <field name="name"/>
                </gantt>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar,gantt</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
```


# 图形视图

```图形视图允许聚合模块的概述和分析,它们的根元素<graph>.。
请注意主视图(元素<pivot>)多维表,在移动到一个图解的概述之前允许文件编写员和维度的选择得到正确的聚合资料组。主视图共享同样的内容定义作为图形视图。
图形视图有4种显示模块,默认模块选择使用the @type属性```


 **Bar(默认)**
```一个条形图,第一个维度是用来在横轴上定义组,其他维度用于在组内定义聚集的bar。
默认情况下bar并排的,通过设置 @stacked = " True "他们可以堆叠到<graph>中

Line
二维线形表格

Pie
二维Pie

图形视图包含<field>，用@type属性强制取值:
行(默认)
在默认情况下，这个字段应该被聚合

Measure
字段应该聚合而不是分组
```
```python
<graph string="Total idea score by Inventor">
    <field name="inventor_id"/>
    <field name="score" type="measure"/>
</graph>
```

**警告**
```图形视图在数据库上执行聚合的值,当被计算的字段值没有存贮空间时将不会运行```


>练习
图形视图添加一个图形视图到会话对象中,对于每一个课程,参加者的数量在一个条形图里面。
1.增加与参与者的数量作为存储计算字段
2.然后添加相关的视图

**openacademy/models.py**
```python
    hours = fields.Float(string="Duration in hours",
                         compute='_get_hours', inverse='_set_hours')

    attendees_count = fields.Integer(
        string="Attendees count", compute='_get_attendees_count', store=True)

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
        for r in self:
            r.duration = r.hours / 24

    @api.depends('attendee_ids')
    def _get_attendees_count(self):
        for r in self:
            r.attendees_count = len(r.attendee_ids)

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
```
**openacademy/views/openacademy.xml**
```python
            </field>
        </record>

        <record model="ir.ui.view" id="openacademy_session_graph_view">
            <field name="name">openacademy.session.graph</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <graph string="Participations by Courses">
                    <field name="course_id"/>
                    <field name="attendees_count" type="measure"/>
                </graph>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar,gantt,graph</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
```

# 看板

```用于组织任务、生产流程等…他们的根元素 <kanban>.
Kanban视图显示一组卡片在列上可能的分组。每张卡片代表了一个记录和每列集聚字段的值。
例如,项目任务可能由阶段(每一列是一个阶段)来分组,或通过负责任（每一列是一个用户)来等等来分组
Kanban视图定义每张卡片的结构为混合的表单元素(包括基本的HTML)和QWeb。```



>练习
Kanban视图
1.添加一个Kanban视图按课程分组显示会话视图(列课程)。
2.添加一个整型的color字段到会话模型添加Kanban视图和更新操作

**openacademy/models.py**
```python
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")
    active = fields.Boolean(default=True)
    color = fields.Integer()

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=['|', ('instructor', '=', True),
```
**openacademy/views/openacademy.xml**
```python
        </record>

        <record model="ir.ui.view" id="view_openacad_session_kanban">
            <field name="name">openacad.session.kanban</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <kanban default_group_by="course_id">
                    <field name="color"/>
                    <templates>
                        <t t-name="kanban-box">
                            <div
                                    t-attf-class="oe_kanban_color_{{kanban_getcolor(record.color.raw_value)}}
                                                  oe_kanban_global_click_edit oe_semantic_html_override
                                                  oe_kanban_card {{record.group_fancy==1 ? 'oe_kanban_card_fancy' : ''}}">
                                <div class="oe_dropdown_kanban">
                                    <!-- dropdown menu -->
                                    <div class="oe_dropdown_toggle">
                                        <i class="fa fa-bars fa-lg"/>
                                        <ul class="oe_dropdown_menu">
                                            <li>
                                                <a type="delete">Delete</a>
                                            </li>
                                            <li>
                                                <ul class="oe_kanban_colorpicker"
                                                    data-field="color"/>
                                            </li>
                                        </ul>
                                    </div>
                                    <div class="oe_clear"></div>
                                </div>
                                <div t-attf-class="oe_kanban_content">
                                    <!-- title -->
                                    Session name:
                                    <field name="name"/>
                                    <br/>
                                    Start date:
                                    <field name="start_date"/>
                                    <br/>
                                    duration:
                                    <field name="duration"/>
                                </div>
                            </div>
                        </t>
                    </templates>
                </kanban>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar,gantt,graph,kanban</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
                  parent="openacademy_menu"
```



# 工作流
工作流是模型相关的业务对象描述他们的动态。工作流也用来跟踪过程经过的时间


>练习
常见的工作流添加一个state字段到会话模型。它将被用来定义一个workflow-ish。
会话可以有三种可能的状态:
草案(默认)，确认和完成。
在会话形式,添加一个(只读)字段,形象化当前状态并用按钮来改变它。有效的转换:
草案- >确认
确认- >草案确认
完成完成- >草案
1.添加一个新的state字段
2.添加状态转换关系方法,，这些方法能够被表格视图调用去改变记录状态
3.添加相关的按钮到会话的表格视图

**openacademy/models.py**
```python
    attendees_count = fields.Integer(
        string="Attendees count", compute='_get_attendees_count', store=True)

    state = fields.Selection([
        ('draft', "Draft"),
        ('confirmed', "Confirmed"),
        ('done', "Done"),
    ], default='draft')

    @api.multi
    def action_draft(self):
        self.state = 'draft'

    @api.multi
    def action_confirm(self):
        self.state = 'confirmed'

    @api.multi
    def action_done(self):
        self.state = 'done'

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
```

**openacademy/views/openacademy.xml**
```python
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <form string="Session Form">
                    <header>
                        <button name="action_draft" type="object"
                                string="Reset to draft"
                                states="confirmed,done"/>
                        <button name="action_confirm" type="object"
                                string="Confirm" states="draft"
                                class="oe_highlight"/>
                        <button name="action_done" type="object"
                                string="Mark as done" states="confirmed"
                                class="oe_highlight"/>
                        <field name="state" widget="statusbar"/>
                    </header>

                    <sheet>
                        <group>
                            <group string="General">
```


```在Odoo中工作流可能与任何对象有关,是完全可定制的。工作流是用于构造和管理业务对象和文档的生命周期,并定义视图转换,触发器等图形工具。工作流活动(节点或操作)和转换(条件)声明为XML记录,像往常一样。在工作流被称作工作条目```


**警告**
工作流模块仅仅只是创建时相关联。因此在工作流定义之前没有与工作流实例相关联的会话实例


>练习
工作流
用一个真正的工作流取代临时会话工作流。转换会话的表单视图用按钮调用工作流，而不是模块的方法。

**openacademy/__openerp__.py**
```python
        'templates.xml',
        'views/openacademy.xml',
        'views/partner.xml',
        'views/session_workflow.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
```

**openacademy/models.py**
```python
        ('draft', "Draft"),
        ('confirmed', "Confirmed"),
        ('done', "Done"),
    ])

    @api.multi
    def action_draft(self):
```
**openacademy/views/openacademy.xml**
```python
            <field name="arch" type="xml">
                <form string="Session Form">
                    <header>
                        <button name="draft" type="workflow"
                                string="Reset to draft"
                                states="confirmed,done"/>
                        <button name="confirm" type="workflow"
                                string="Confirm" states="draft"
                                class="oe_highlight"/>
                        <button name="done" type="workflow"
                                string="Mark as done" states="confirmed"
                                class="oe_highlight"/>
                        <field name="state" widget="statusbar"/>
```
**openacademy/views/session_workflow.xml**
```python
<openerp>
    <data>
        <record model="workflow" id="wkf_session">
            <field name="name">OpenAcademy sessions workflow</field>
            <field name="osv">openacademy.session</field>
            <field name="on_create">True</field>
        </record>

        <record model="workflow.activity" id="draft">
            <field name="name">Draft</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="flow_start" eval="True"/>
            <field name="kind">function</field>
            <field name="action">action_draft()</field>
        </record>
        <record model="workflow.activity" id="confirmed">
            <field name="name">Confirmed</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">function</field>
            <field name="action">action_confirm()</field>
        </record>
        <record model="workflow.activity" id="done">
            <field name="name">Done</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">function</field>
            <field name="action">action_done()</field>
        </record>

        <record model="workflow.transition" id="session_draft_to_confirmed">
            <field name="act_from" ref="draft"/>
            <field name="act_to" ref="confirmed"/>
            <field name="signal">confirm</field>
        </record>
        <record model="workflow.transition" id="session_confirmed_to_draft">
            <field name="act_from" ref="confirmed"/>
            <field name="act_to" ref="draft"/>
            <field name="signal">draft</field>
        </record>
        <record model="workflow.transition" id="session_done_to_draft">
            <field name="act_from" ref="done"/>
            <field name="act_to" ref="draft"/>
            <field name="signal">draft</field>
        </record>
        <record model="workflow.transition" id="session_confirmed_to_done">
            <field name="act_from" ref="confirmed"/>
            <field name="act_to" ref="done"/>
            <field name="signal">done</field>
        </record>
    </data>
</openerp>
```




**提示**
```为了检查工作流实例是否在会话旁边正确创建,去设置Settings ‣Technical ‣ Workflows ‣ Instances```


>练习
自动翻译从草稿到确认的会话，超多一半的会话的位置是被保留的

**openacademy/views/session_workflow.xml**
```python
            <field name="act_to" ref="done"/>
            <field name="signal">done</field>
        </record>

        <record model="workflow.transition" id="session_auto_confirm_half_filled">
            <field name="act_from" ref="draft"/>
            <field name="act_to" ref="confirmed"/>
            <field name="condition">taken_seats &gt; 50</field>
        </record>
    </data>
</openerp>
```


>练习
服务器操作
为了同步会话状态，通过服务器操作取代python方法
工作流和服务器操作完全可以从UI中被创建的。

**openacademy/views/session_workflow.xml**
```python
            <field name="on_create">True</field>
        </record>

        <record model="ir.actions.server" id="set_session_to_draft">
            <field name="name">Set session to Draft</field>
            <field name="model_id" ref="model_openacademy_session"/>
            <field name="code">
model.search([('id', 'in', context['active_ids'])]).action_draft()
            </field>
        </record>
        <record model="workflow.activity" id="draft">
            <field name="name">Draft</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="flow_start" eval="True"/>
            <field name="kind">dummy</field>
            <field name="action"></field>
            <field name="action_id" ref="set_session_to_draft"/>
        </record>

        <record model="ir.actions.server" id="set_session_to_confirmed">
            <field name="name">Set session to Confirmed</field>
            <field name="model_id" ref="model_openacademy_session"/>
            <field name="code">
model.search([('id', 'in', context['active_ids'])]).action_confirm()
            </field>
        </record>
        <record model="workflow.activity" id="confirmed">
            <field name="name">Confirmed</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">dummy</field>
            <field name="action"></field>
            <field name="action_id" ref="set_session_to_confirmed"/>
        </record>

        <record model="ir.actions.server" id="set_session_to_done">
            <field name="name">Set session to Done</field>
            <field name="model_id" ref="model_openacademy_session"/>
            <field name="code">
model.search([('id', 'in', context['active_ids'])]).action_done()
            </field>
        </record>
        <record model="workflow.activity" id="done">
            <field name="name">Done</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">dummy</field>
            <field name="action"></field>
            <field name="action_id" ref="set_session_to_done"/>
        </record>

        <record model="workflow.transition" id="session_draft_to_confirmed">
```


# 安全
```必须配置访问控制机制来实现一个一致的安全策略。```


## 组的访问控制机制
```组被创建为一个正常的记录在模块的res.groups中，菜单的权限通过菜单定义。然而即使没有菜单,对象可能仍可间接访问,所以实际对象权限级别(读、写、创建、分离)必须定义为组。他们通常在模块内部插入CSV文件。在一个视图或对象中使用字段组的属性也可以限制对特定字段的访问```


## 访问权限
```访问权限被定义为ir.model.access的模块记录。每个访问权限都与模块相关,一组(或没有组为全局访问),设置一个权限:读、写、删除、分离。这样的访问权限通常是由一个CSV文件命名为他自己的模块:ir.model.access.csv。```
```python
id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
access_idea_idea,idea.idea,model_idea_idea,base.group_user,1,1,1,0
access_idea_vote,idea.vote,model_idea_vote,base.group_user,1,1,1,0
```


>练习
添加访问控制通过OpenERP接口创建一个新用户"John Smith"。然后创建一个组"OpenAcademy / Session Read"会话模块的权限为读
1.创建一个新的用户John Smith通过Settings ‣ Users ‣ Users
2.创建一个新组session_read 通过Settings ‣ Users ‣ Groups,会话模块的权限是读
 3.编辑John Smith,让他成为session_read中的成员
4.以John Smith登录检查访问权限是正确的

>练习
通过数据文件添加访问控制使用数据文件
创建一个组OpenAcademy / Manager具有完全访问所有OpenAcademy模块的权限
让所有用户对会话和课程可读
1.创建一个新文件openacademy/security/security.xml去掌管OpenAcademy Manage组
2.编辑该文件openacademy/security/ir.model.access.csv模块的访问权限的
3.最后更新openacademy/__openerp__.py添加新的数据文件

**openacademy/__openerp__.py**
```python

    # always loaded
    'data': [
        'security/security.xml',
        'security/ir.model.access.csv',
        'templates.xml',
        'views/openacademy.xml',
        'views/partner.xml',
```

**openacademy/security/ir.model.access.csv**
```python
id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
course_manager,course manager,model_openacademy_course,group_manager,1,1,1,1
session_manager,session manager,model_openacademy_session,group_manager,1,1,1,1
course_read_all,course all,model_openacademy_course,,1,0,0,0
session_read_all,session all,model_openacademy_session,,1,0,0,0
openacademy/security/security.xml
<openerp>
    <data>
        <record id="group_manager" model="res.groups">
            <field name="name">OpenAcademy / Manager</field>
        </record>
    </data>
</openerp>
```


## 记录规则
```给定模块的的子集的访问控制权限由模块规则限制。规则是模块的一个记录ir.rule并且与一些模型相关,一些团体(many2many字段),权限的限制,申请，一个域。记录访问权限的域指定哪些权限是被限制的。
这里是一个阻止删除领导的规则不再cancel状态的一个例子,。注意，group字段的值组必须遵循相同的约定：ORM的write()方法。```
```python
<record id="delete_cancelled_only" model="ir.rule">
    <field name="name">Only cancelled leads may be deleted</field>
    <field name="model_id" ref="crm.model_crm_lead"/>
    <field name="groups" eval="[(4, ref('base.group_sale_manager'))]"/>
    <field name="perm_read" eval="0"/>
    <field name="perm_write" eval="0"/>
    <field name="perm_create" eval="0"/>
    <field name="perm_unlink" eval="1" />
    <field name="domain_force">[('state','=','cancel')]</field>
</record>
```

>练习
记录规则
为模块添加一个记录规则,对课程和组"OpenAcademy / Manager"所负责的课程限制编写和删除权限。如果没有人负责这门课程,这个组所有用户必须能够修改它。
创建一个新的规则openacademy/security/security.xml:

**openacademy/security/security.xml**
```python
        <record id="group_manager" model="res.groups">
            <field name="name">OpenAcademy / Manager</field>
        </record>

        <record id="only_responsible_can_modify" model="ir.rule">
            <field name="name">Only Responsible can modify Course</field>
            <field name="model_id" ref="model_openacademy_course"/>
            <field name="groups" eval="[(4, ref('openacademy.group_manager'))]"/>
            <field name="perm_read" eval="0"/>
            <field name="perm_write" eval="1"/>
            <field name="perm_create" eval="0"/>
            <field name="perm_unlink" eval="1"/>
            <field name="domain_force">
                ['|', ('responsible_id','=',False),
                      ('responsible_id','=',user.id)]
            </field>
        </record>
    </data>
</openerp>
```

# 向导
```向导通过动态形式向用户描述交互式会话(或对话框)。向导是一个简单的模型,继承了TransientModelERB而不是，TransientModel类继承了model重用了他所有的机制,具有以下特性:
 向导记录不是持久的,一段时间后会自动从数据库中删除。这就是为什么他们短暂的原因
 向导模型不需要显式的访问权限:在向导记录中用户拥有所有权限。
 向导记录可能通过many2one字段查阅规则记录或者向导记录,但是规则记录不能通过many2one字段查阅向导字段。
我们想要创建一个向导,允许用户为特定的会话创建参与者,或会话列表。```


>练习
定义向导
创建一个向导模块用many2one关系的会话模块和伙伴模块。
添加一个新文件openacademy / wizard.py:

**openacademy/__init__.py**
```python
from . import controllers
from . import models
from . import partner
from . import wizard
```
**openacademy/wizard.py**
```python
# -*- coding: utf-8 -*-

from openerp import models, fields, api

class Wizard(models.TransientModel):
    _name = 'openacademy.wizard'

    session_id = fields.Many2one('openacademy.session',
        string="Session", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

```

## 启动向导
```向导通过ir.actions.act_window记录来发布。用target字段来设置它的值。后打开向导视图在一个弹出窗口里。这个操作可以通过菜单项来触发。
还可以用另一种方式来启动向导: 像上面一样，使用一个ir.actions.act_window，但需要用一个额外的src_model字段指定哪个模块的上下文的操作可行。上面的主要的视图的向导将出现在模块的上下文操作中，。因为在ORM中一些内部关系,这种行动声明在XML中标签为act_window。```
python
<act_window id="launch_the_wizard"
            name="Launch the Wizard"
            src_model="context.model.name"
            res_model="wizard.model.name"
            view_mode="form"
            target="new"
            key2="client_action_multi"/>
```

```向导使用规则的视图和他们的按钮可以使用special="cancel"属性，去关闭还没保存的向导窗口。```

>练习
运行向导
1. 为向导定义一个表格视图
2. 在会话模块的文本中添加操作运行向导
3. 在向导中为会话字段设置默认值，使用self._context参数去检索当前会话

**openacademy/wizard.py**
```python
class Wizard(models.TransientModel):
    _name = 'openacademy.wizard'

    def _default_session(self):
        return self.env['openacademy.session'].browse(self._context.get('active_id'))

    session_id = fields.Many2one('openacademy.session',
        string="Session", required=True, default=_default_session)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
openacademy/views/openacademy.xml
                  parent="openacademy_menu"
                  action="session_list_action"/>

        <record model="ir.ui.view" id="wizard_form_view">
            <field name="name">wizard.form</field>
            <field name="model">openacademy.wizard</field>
            <field name="arch" type="xml">
                <form string="Add Attendees">
                    <group>
                        <field name="session_id"/>
                        <field name="attendee_ids"/>
                    </group>
                </form>
            </field>
        </record>

        <act_window id="launch_session_wizard"
                    name="Add Attendees"
                    src_model="openacademy.session"
                    res_model="openacademy.wizard"
                    view_mode="form"
                    target="new"
                    key2="client_action_multi"/>
    </data>
</openerp>

```

>练习
与会者登记
将按钮添加到向导,并实现相应的方法将与会者添加到给定会话。

**openacademy/views/openacademy.xml**
```python
                        <field name="attendee_ids"/>
                    </group>
                    <footer>
                        <button name="subscribe" type="object"
                                string="Subscribe" class="oe_highlight"/>
                        or
                        <button special="cancel" string="Cancel"/>
                    </footer>
                </form>
            </field>
        </record>
```

**openacademy/wizard.py**
```python
    session_id = fields.Many2one('openacademy.session',
        string="Session", required=True, default=_default_session)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    @api.multi
    def subscribe(self):
        self.session_id.attendee_ids |= self.attendee_ids
        return {}
```

>练习
与会者登记到多个会话
修改向导模块,使与会者能够在多个会话中注册

**openacademy/views/openacademy.xml**
```python
                <form string="Add Attendees">
                    <group>
                        <field name="session_ids"/>
                        <field name="attendee_ids"/>
                    </group>
                    <footer>
                        <button name="subscribe" type="object"
```
**openacademy/wizard.py**
```python
class Wizard(models.TransientModel):
    _name = 'openacademy.wizard'

    def _default_sessions(self):
        return self.env['openacademy.session'].browse(self._context.get('active_ids'))

    session_ids = fields.Many2many('openacademy.session',
        string="Sessions", required=True, default=_default_sessions)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    @api.multi
    def subscribe(self):
        for session in self.session_ids:
            session.attendee_ids |= self.attendee_ids
        return {}
```


# 国际化
```每个模块可以在i18n文件夹内通过名为LANG.po的文件提供自己的翻译,，区域代码的语言是朗,语言和国家结合，当他们不同时(如pt.po或pt_BR.po)。翻译将自动加载Odoo启用所有可用的语言。开发人员总是用英语来创建一个模块,然后使用Odoo gettext导出模块项目，导出出口的特性(Settings ‣ Translations ‣ Import/Export ‣ Export Translation没有指定语言),创建模块模板POT文件,然后推导出翻译PO文件。用系统集成的的插件或模块进行编辑和合并PO /POT文件。```


**提示:Odoo生成的可移植的对象文件产生在Transifex中,便于翻译软件。**
```python
|- idea/ # The module directory
   |- i18n/ # Translation files
      | - idea.pot # Translation Template (exported from Odoo)
      | - fr.po # French translation
      | - pt_BR.po # Brazilian Portuguese translation
      | (...)
```

**提示:
默认情况下，在python中Odoo 的POT的出口只提取标签在XML文件或在字段定义中,但任何Python字符串都被周围的openerp._()函数翻译(例如_"Label")**

>练习
翻译一个模块
选择另外一门语言安装Odoo。使用Odoo提供的工具翻译你的 模块
1.创建一个文件夹openacademy / i18n /
2.安装你想安装的语言(Administration ‣ Translations ‣ Load an Official Translation)
3.同步可翻译术语(Administration ‣ Translations ‣ Application Terms ‣ Synchronize Translations)
4. 通过出口创建一个模板翻译文件 (Administration ‣ Translations -> Import/Export ‣ Export Translation)         语言没有限制,保存到inopenacademy / i18n /中
5.创建一个翻译文件 (Administration ‣ Translations ‣ Import/Export ‣ Export Translation)并指定一种语言。保存到inopenacademy / i18n /中
6.打开导出的翻译文件(用一个基本的文本编辑器或专用的PO文件编辑器例如POEdit编辑和翻译缺失的术语
7.在models.py为openerp._，标记缺失的字符串为可译的
 8.重复步骤3 - 6

**openacademy/models.py**
```python
# -*- coding: utf-8 -*-

from datetime import timedelta
from openerp import models, fields, api, exceptions, _

class Course(models.Model):
    _name = 'openacademy.course'
        default = dict(default or {})

        copied_count = self.search_count(
            [('name', '=like', _(u"Copy of {}%").format(self.name))])
        if not copied_count:
            new_name = _(u"Copy of {}").format(self.name)
        else:
            new_name = _(u"Copy of {} ({})").format(self.name, copied_count)

        default['name'] = new_name
        return super(Course, self).copy(default)
        if self.seats < 0:
            return {
                'warning': {
                    'title': _("Incorrect 'seats' value"),
                    'message': _("The number of available seats may not be negative"),
                },
            }
        if self.seats < len(self.attendee_ids):
            return {
                'warning': {
                    'title': _("Too many attendees"),
                    'message': _("Increase seats or remove excess attendees"),
                },
            }

    def _check_instructor_not_in_attendees(self):
        for r in self:
            if r.instructor_id and r.instructor_id in r.attendee_ids:
                raise exceptions.ValidationError(_("A session's instructor can't be an attendee"))
```


# 报告
## 打印报告
```Odoo 8.0附带一份基于QWeb,Twitter Bootstrap and Wkhtmltopdf新的报告引导.。
报告是一个组合两个元素:
一个ir.actions.report.xml, <report>提供快捷元素,它设置各种报告的基本参数(默认类型,继承后的保存路径)```
```python
<report
    id="account_invoices"
    model="account.invoice"
    string="Invoices"
    report_type="qweb-pdf"
    name="account.report_invoice"
    file="account.report_invoice"
    attachment_use="True"
    attachment="(object.state in ('open','paid')) and
        ('INV'+(object.number or '').replace('/','')+'.pdf')"
/>
```

```一个标准的QWeb视图实际报告:```
```python
<t t-call="report.html_container">
    <t t-foreach="docs" t-as="o">
        <t t-call="report.external_layout">
            <div class="page">
                <h2>Report title</h2>
            </div>
        </t>
    </t>
</t>

the standard rendering context provides a number of elements, the most
important being:

``docs``
    the records for which the report is printed
``user``
    the user printing the report
```

```因为报告是标准的web页面,它们可以通过一个URL得到可以通过这个URL操纵输出参数,例如HTML版本的发票报告可以通过http://localhost:8069/report/html/account.report_invoice/1来得到(如果account被注册)和PDF版本通过http://localhost:8069/report/pdf/account.report_invoice/1.```


**危险**
如果你发现你的PDF报告没有布局（IE中可以看到文本但是布局和html中的版本不一样）很可能就是因为你的wkhtmltopdf过程不能到达web服务器去下载他们
生成PDF报告时，如果你的服务器和CSS式样没有被下载,，就一定会出问题，你的wkhtmltopdf过程无法到达服务器去下载他们你的wkhtmltopdf过程将使用wkhtmltopdf系统参数作为根路径去连接相关文件,但该参数会在每次管理员登录时自动更新。如果您的服务器无法支持这种代理模式,。你可以通过添加这些系统参数中的一个来修补。
report.url指向一个您的服务器可以到达的url(可以是http://localhost:8069或类似的)。它仅仅只能用于这个目的。
web.base.url.freeze,当设置为True时,将停止web.base.url的自动更新。```


>练习
为会话模块创建一个报告
对于每一个会话,它应该显示会话的名字,会话的开始和结束,会议的参与者名单。

**openacademy/__openerp__.py**
```python
        'views/openacademy.xml',
        'views/partner.xml',
        'views/session_workflow.xml',
        'reports.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
```
**openacademy/reports.xml**
```python
<openerp>
<data>
    <report
        id="report_session"
        model="openacademy.session"
        string="Session Report"
        name="openacademy.report_session_view"
        file="openacademy.report_session"
        report_type="qweb-pdf" />

    <template id="report_session_view">
        <t t-call="report.html_container">
            <t t-foreach="docs" t-as="doc">
                <t t-call="report.external_layout">
                    <div class="page">
                        <h2 t-field="doc.name"/>
                        <p>From <span t-field="doc.start_date"/> to <span t-field="doc.end_date"/></p>
                        <h3>Attendees:</h3>
                        <ul>
                            <t t-foreach="doc.attendee_ids" t-as="attendee">
                                <li><span t-field="attendee.name"/></li>
                            </t>
                        </ul>
                    </div>
                </t>
            </t>
        </t>
    </template>
</data>
</openerp>
```

## 指示板
>练习
定义一个指示板
定义一个指示板包含你创建的图形视图,会议日历视图和课程列表视图 (可转换为表单视图)。这个显示板应该能够通过菜单项里面的子菜单得到,并且当OpenAcademy主菜单时被选中自动显示在web客户端里面。
1.创建一个openacademy/views/session_board.xml文件，它应该包含板示功能, 一个操作打开指示板并且重定义主菜单项来添加指示板操作
请注意
可用指示板样式1,1 - 1、1 - 2、2 - 1、1-1-1
2.更新openacademy/__openerp__.py引用新的数据文件

**openacademy/__openerp__.py**
```python
    'version': '0.1',

    # any module necessary for this one to work correctly
    'depends': ['base', 'board'],

    # always loaded
    'data': [
        'views/openacademy.xml',
        'views/partner.xml',
        'views/session_workflow.xml',
        'views/session_board.xml',
        'reports.xml',
    ],
    # only loaded in demonstration mode
```
**openacademy/views/session_board.xml**
```python
<?xml version="1.0"?>
<openerp>
    <data>
        <record model="ir.actions.act_window" id="act_session_graph">
            <field name="name">Attendees by course</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">graph</field>
            <field name="view_id"
                   ref="openacademy.openacademy_session_graph_view"/>
        </record>
        <record model="ir.actions.act_window" id="act_session_calendar">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">calendar</field>
            <field name="view_id" ref="openacademy.session_calendar_view"/>
        </record>
        <record model="ir.actions.act_window" id="act_course_list">
            <field name="name">Courses</field>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
        </record>
        <record model="ir.ui.view" id="board_session_form">
            <field name="name">Session Dashboard Form</field>
            <field name="model">board.board</field>
            <field name="type">form</field>
            <field name="arch" type="xml">
                <form string="Session Dashboard">
                    <board style="2-1">
                        <column>
                            <action
                                string="Attendees by course"
                                name="%(act_session_graph)d"
                                height="150"
                                width="510"/>
                            <action
                                string="Sessions"
                                name="%(act_session_calendar)d"/>
                        </column>
                        <column>
                            <action
                                string="Courses"
                                name="%(act_course_list)d"/>
                        </column>
                    </board>
                </form>
            </field>
        </record>
        <record model="ir.actions.act_window" id="open_board_session">
          <field name="name">Session Dashboard</field>
          <field name="res_model">board.board</field>
          <field name="view_type">form</field>
          <field name="view_mode">form</field>
          <field name="usage">menu</field>
          <field name="view_id" ref="board_session_form"/>
        </record>

        <menuitem
            name="Session Dashboard" parent="base.menu_reporting_dashboard"
            action="open_board_session"
            sequence="1"
            id="menu_board_session" icon="terp-graph"/>
    </data>
</openerp>
```


# web服务
web服务模为所有web服务的块提供一个公共接口:
xml - rpc
json - rpc
也可以通过分布式对象机制访问业务对象。他们都可以通过客户端接口用上下文的视图来进行修改。
Odoo可以通过xml - rpc / json - rpc接口来访问,库中存在多种语言。


## xml - rpc库
下面的例子是一个Python程序, 通过xmlrpclib与一个odoo服务器连接
```Python
import xmlrpclib

root = 'http://%s:%d/xmlrpc/' % (HOST, PORT)

uid = xmlrpclib.ServerProxy(root + 'common').login(DB, USER, PASS)
print "Logged in as %s (uid: %d)" % (USER, uid)

# Create a new note
sock = xmlrpclib.ServerProxy(root + 'object')
args = {
    'color' : 8,
    'memo' : 'This is a note',
    'create_uid': uid,
}
note_id = sock.execute(DB, uid, PASS, 'note.note', 'create', args)
```


>练习
添加一个新的服务到客户端
写一个可以发送xml - rpc请求到个人电脑的Python程序，运行的Odoo(你的,或者你导师的)。这个程序应该显示所有的会话,以及相应的位置。最少为一门课程创建一个新会话

```Python
import functools
import xmlrpclib
HOST = 'localhost'
PORT = 8069
DB = 'openacademy'
USER = 'admin'
PASS = 'admin'
ROOT = 'http://%s:%d/xmlrpc/' % (HOST,PORT)

# 1. Login
uid = xmlrpclib.ServerProxy(ROOT + 'common').login(DB,USER,PASS)
print "Logged in as %s (uid:%d)" % (USER,uid)

call = functools.partial(
    xmlrpclib.ServerProxy(ROOT + 'object').execute,
    DB, uid, PASS)

# 2. Read the sessions
sessions = call('openacademy.session','search_read', [], ['name','seats'])
for session in sessions:
    print "Session %s (%s seats)" % (session['name'], session['seats'])
# 3.create a new session
session_id = call('openacademy.session', 'create', {
    'name' : 'My session',
    'course_id' : 2,
})
```

而不是使用硬编码的课程id,代码可以通过名称查找
```python
# 3.create a new session for the "Functional" course
course_id = call('openacademy.course', 'search', [('name','ilike','Functional')])[0]
session_id = call('openacademy.session', 'create', {
    'name' : 'My session',
    'course_id' : course_id,
})
```

## json - rpc库
下面的例子是一个Python程序, 用Python库urllib2和json作为标准与一个Odoo服务器交互
```python
import json
import random
import urllib2

def json_rpc(url, method, params):
    data = {
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": random.randint(0, 1000000000),
    }
    req = urllib2.Request(url=url, data=json.dumps(data), headers={
        "Content-Type":"application/json",
    })
    reply = json.load(urllib2.urlopen(req))
    if reply.get("error"):
        raise Exception(reply["error"])
    return reply["result"]

def call(url, service, method, *args):
    return json_rpc(url, "call", {"service": service, "method": method, "args": args})

# log in the given database
url = "http://%s:%s/jsonrpc" % (HOST, PORT)
uid = call(url, "common", "login", DB, USER, PASS)

# create a new note
args = {
    'color' : 8,
    'memo' : 'This is another note',
    'create_uid': uid,
}
note_id = call(url, "object", "execute", DB, uid, PASS, 'note.note', 'create', args)

```

```这是同一个程序,使用jsonrpclib库:```

```python
import jsonrpclib

# server proxy object
url = "http://%s:%s/jsonrpc" % (HOST, PORT)
server = jsonrpclib.Server(url)

# log in the given database
uid = server.call(service="common", method="login", args=[DB, USER, PASS])

# helper function for invoking model methods
def invoke(model, method, *args):
    args = [DB, uid, PASS, model, method] + list(args)
    return server.call(service="object", method="execute", args=args)

# create a new note
args = {
    'color' : 8,
    'memo' : 'This is another note',
    'create_uid': uid,
}
note_id = invoke('note.note', 'create', args)
```


```这些例子可以很容易地改编自xml - rpc j运用到son – rpc中。```
**请注意**
有许多高级api在不同语言来访问Odoo系统没有显式地通过xml - rpc或json - rpc,如:
(https://github.com/akretion/ooor)
(https://github.com/syleam/openobject-library)
(https://github.com/nicolas-van/openerp-client-lib)
(https://pypi.python.org/pypi/oersted/)
(https://github.com/abhishek-jaiswal/php-openerp-lib)
```[1]可以禁用一些字段的自动创建
[2]写原始SQL查询是可能的,但是需要注意,因为它没有任何Odoo身份验证和安全机制```
