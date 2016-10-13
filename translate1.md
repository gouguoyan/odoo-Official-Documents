---------------
[TOC]

# 启动停止odoo服务器
```Odoo使用客户端/服务器体系结构,客户端浏览器通过RPC服务器访问Odoo。
业务逻辑和扩展通常是在服务器端执行,尽管支持客户端特性(如交互式地图等新数据表示)可以添加到客户端。
为了启动服务器,只需调用命令odoo.py[shell]必要时添加到文件的完整路径```
```python
odoo.py
```
```按**ctrl - c**两次停止服务器,或结束相应的进程```


# 建立一个odoo模块

```服务器和客户端扩展都打包成可选择载入进数据库的模块。Odoo模块也可以添加Odoo系统全新的业务逻辑,或改变和扩展现有业务逻辑:一个模块可以添加创建你的国家的会计准则Odoo通用会计支持,而下一个模块添加到一个支持实时可视化队列中。一切与模块Odoo从而开始和结束。```


 ```一个模块的组成
一个Odoo模块可以包含许多元素:
业务对象
        <!--   <record id="object1" model="openacademy.openacademy"> -->
        <!--     <f
声明为Python类,这些资源基于它们的配置不断的自动保存到Odoo
数据文件
XML或CSV文件声明基础数据(视图或工作流),配置数据(参数化模块),演示数据等等
网络控制器
处理来自web浏览器的请求
静态web数据
网页界面或者网站使用的图像，css或javascript文件```


## 模块结构
```每个模块都是一个单独的文件夹。通过使用——addons-pathoption指定目录。
大多数命令行选项也可以使用配置文件设置声明一个Odoo模块清单。请参阅清单文档信息。一个模块也是 __init__.py文件,包含为Python导入各种说明（指令）文件指令在这个模块里面。例如,如果模块只有一个mymodule.py文件__init__.py包含:```

```Python
from . import mymodule
```

```Odoo提供了一种机制来帮助建立一个新的模块Odoo.Py有子命令的搭建去创建一个空的模块:```

```Python
$ odoo.py scaffold <module name> <where to put it>
```

```命令为模块创建一个子目录,并为模块自动创建一些标准文件。他们中的大多数只是包含注释代码或XML。这些文件大部分的使用方法将会被单独解释和指导```

>练习
模块的创建
使用上面的命令行创建一个空模块Open Academy,并在Odoo安装它。
1. 调用odoo.py命令搭建openacademy插件。
2. 调整manifest文件以指向到模块。
3. 不要修改其他文件。  

**openacademy/__openerp__.py**

```python
# -*- coding: utf-8 -*-
{
    'name': "Open A
        <!--   <record id="object1" model="openacademy.openacademy"> -->
        <!--     <fcademy",

    'summary': """Manage trainings""",

    'description': """
        Open Academy module for managing trainings:
            - training courses
            - training sessions
            - attendees registration
    """,

    'author': "My Company",
    'website': "http://www.yourcompany.com",

    # Categories can be used to filter modules in modules listing
    # Check https://github.com/odoo/odoo/blob/master/openerp/addons/base/module/module_data.xml
    # for the full list
    'category': 'Test',
    'version': '0.1',

    # any module necessary for this one to work correctly
    'depends': ['base'],

    # always loaded
    'data': [
        # 'security/ir.model.access.csv',
        'templates.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
        'demo.xml',
    ],
}
```

**openacademy/__init__.py**

```python
# -*- coding: utf-8 -*-
from . import controllers
from . import models
openacademy/controllers.py
# -*- coding: utf-8 -*-
from openerp import http

# class Openacademy(http.Controller):
#     @http.route('/openacademy/openacademy/', auth='public')
#     def index(self, **kw):
#         return "Hello, world"

#     @http.route('/openacademy/openacademy/objects/', auth='public')
#     def list(self, **kw):
#         return http.request.render('openacademy.listing', {
#             'root': '/openacademy/openacademy',
#             'objects': http.request.env['openacademy.openacademy'].search([]),
#         })

#     @http.route('/openacademy/openacademy/objects/<model("openacademy.openacademy"):obj>/', auth='public')
#     def object(self, obj, **kw):
#         return http.request.render('openacademy.object', {
#             'object': obj
#         })
```
**openacademy/demo.xml**

```python
<openerp>
    <data>
        <!--  -->
        <!--   <record id="object0" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 0</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object1" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 1</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object2" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 2</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object3" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 3</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object4" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 4</field> -->
        <!--   </record> -->
        <!--  -->
    </data>
</openerp>
```

**openacademy/models.py**
```python
# -*- coding: utf-8 -*-

from openerp import models, fields, api

# class openacademy(models.Model):
#     _name = 'openb, legal, img, l, i, code, t, tableacademy.openacademy'

#     name = fields.Char()
```
**openacademy/security/ir.model.access.csv**
```python
id,name,model_id/idb, legal, img, l, i, code, t, table,group_id/id,perm_read,perm_write,perm_create,perm_unlink
access_openacademy_openacademy,openacademy.openacademy,model_openacademy_openacademy,,1,0,0,0
openacademy/templates.xml
<openerp>
    <data>
        <!-- <template id="listing"> -->
        <!--   <ul> -->
        <!--     <li t-foreach="objects" t-as="object"> -->
        <!--       <a t-attf-href="{{ root }}/objects/{{ object.id }}"> -->
        <!--         <t t-esc="object.display_name"/> -->
        <!--       </a> -->
        <!--     </li> -->
        <!--   </ul> -->
        <!-- </template> -->
        <!-- <template id="object"> -->
        <!--   <h1><t t-esc="object.display_name"/></h1> -->
        <!--   <dl> -->
        <!--     <t t-foreach="object._fields" t-as="field"> -->
        <!--       <dt><t t-esc="field"/></dt> -->
        <!--       <dd><t t-esc="object[field]"/></dd> -->
        <!--     </t> -->
        <!--   </dl> -->
        <!-- </template> -->
    </data>
</openerp>
```


## 对象关系映射
```一个关键组成部分Odoo是ORM层。这一层避免不得不手写大多数SQL,services2提供可扩展性和安全性。（英语：Object Relation Mapping，对象关系映射，简称ORM）
业务对象被声明为继承于Model的python类，并集成于自动维护系统中。
模型可以通过设置一些配置属性的定义。name是最重要的属性，在Odoo的系统中name是必须要定义的。这是一个最低限度的完整定义模型:```

```python
from openerp import models
class MinimalModel(models.Model):
    _name = 'test.model'
```


**模型域**
```域是用于定义模型存储的位置和类型。在模型类里面字段定义为属性```

```python
from openerp import models, fields

class LessMinimalModel(models.Model):
    _name = 'test.model2'

    name = fields.Char()
    ```


## 公共属性
```模型本身一样,通过配置属性的参数可以配置字段```
```python
name = field.Char(required=True)
```

```一些属性是可用的所有字段,这里是最常见的:```

```string (unicode, default: field's name)

The label of the field in UI (visible by users).

required (bool, default: False)

If True, the field can not be empty, it must either have a default value or always be given a value when creating a record.

help (unicode, default: '')

Long-form, provides a help tooltip to users in the UI.

index (bool, default: False)

Requests that Odoo create a database index on the column


Requests that Odoo create a  database  index  on the column
```


## 简单的字段
```有两大类:“简单”字段原始的值直接存储在模块的表格中，“关系”字段链接记录(相同的模块或不同的模块```


```举一个例子，简单的字段有Boolean，Date，Char```


## 保留字段

```Odoo在所有在所有模块下创建几个字段。这些字段由系统管理,并且没有写的权限，但如果有需要可以读```

```id (Id)
the unique identifier for a record in its model
create_date (Datetime)
creation date of the record
create_uid (Many2one)
user who created the record
write_date (Datetime)
last modification date of the record
write_uid (Many2one)
user who last modified the record```


## 特殊的字段

```默认的，odoo在所有模块中都需要一个name字段来显示或者搜索，也可以重写为_rec_name```


>练习
定义一个模型
在openacademy模块定义一个新的数据模型。一门课程有一个标题和描述。课程必须有一个标题。
编辑文件openacademy / models.py包括课程类。

**openacademy/models.py**

from openerp import models, fields, api

class Course(models.Model):
    _name = 'openacademy.course'

    name = fields.Char(string="Title", required=True)
    description = fields.Text()
```

## 数据文件
```Odoo驱动系统是一个高级数据驱动系统。虽然表现自定义为使用Python代码模块的一部分的值在它创建的时候加载。
提示一些模块仅仅只有往odoo中添加数据的作用
模块数据通过数据文件和带<record>标签的xml文件声明。每个<record>元素创建或更新一条数据库记录。```
```Python
<openerp>
    <data>
        <record model="{model name}" id="{record identifier}">
            <field name="{a field name}">{a value}</field>
        </record>
    </data>
</openerp>
```


```模型的名字是Odoo模块的备案id是一个外部标识符,可以用它来指定记录(不需要知道它的数据库内的标识符)
<field>元素有一个名称，他是该字段在模块里面的名称（如description）。后面跟着字段的值。
数据文件必须在菜单文件中定义之后才能被加载，他们可以在'data'列表中声明(总是加载)或“演示”列表中声明(仅在演示模块下加载)。```


>练习
定义演示数据
创建示范数据填充一些示范课程的课程模型。
编辑包含一些数据的文件openacademy/demo.xml

**openacademy/demo.xml**
```python
<openerp>
    <data>
        <record model="openacademy.course" id="course0">
            <field name="name">Course 0</field>
            <field name="description">Course 0's description

Can have multiple lines
            </field>
        </record>
        <record model="openacademy.course" id="course1">
            <field name="name">Course 1</field>
            <!-- no description for this one -->
        </record>
        <record model="openacademy.course" id="course2">
            <field name="name">Course 2</field>
            <field name="description">Course 2's description</field>
        </record>
    </data>
</openerp>
```


## 操作和菜单

```操作和菜单定期记录数据库,通常通过数据文件来定义，可以触发在三个行为
1.通过点击菜单项(链接到具体操作)
2.通过点击按钮视图(如果这些试图按钮是连接到操作的)
3.上下文对象上的动作
因为菜单声明有些复杂，<menuitem>声明一个ir.ui的捷径连接到相应的操作更容易。```
```python
<record model="ir.actions.act_window" id="action_list_ideas">
    <field name="name">Ideas</field>
    <field name="res_model">idea.idea</field>
    <field name="view_mode">tree,form</field>
</record>
<menuitem id="menu_ideas" parent="menu_root" name="Ideas" sequence="10"
          action="action_list_ideas"/>
```


**危险**

```操作之前必须在XML文件中申明其相应的菜单
数据文件按顺序执行, 操作的id必须出现在之前的数据库在创建菜单之前。```

>练习
定义新的菜单项
定义新的菜单项访问OpenAcademy菜单项下的课程。用户应该能够
显示一个列表下的所有课程
创建/修改课程
1.创建openacademy/views/openacademy.xml用一个动作菜单触发动作
2.将其添加到openacademy / __openerp__.py的数据列表中

```python
openacademy/__openerp__.py
    'data': [
        # 'security/ir.model.access.csv',
        'templates.xml',
        'views/openacademy.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
openacademy/views/openacademy.xml
<?xml version="1.0" encoding="UTF-8"?>
<openerp>
    <data>
        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
            that is an action opening a view or a set of views
        -->
        <record model="ir.actions.act_window" id="course_list_action">
            <field name="name">Courses</field>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
            <field name="help" type="html">
                <p class="oe_view_nocontent_create">Create the first course
                </p>
            </field>
        </record>

        <!-- top level menu: no parent -->
        <menuitem id="main_openacademy_menu" name="Open Academy"/>
        <!-- A first level in the left side menu is needed
             before using action= attribute -->
        <menuitem id="openacademy_menu" name="Open Academy"
                  parent="main_openacademy_menu"/>
        <!-- the following menuitem should appear *after*
             its parent openacademy_menu and *after* its
             action course_list_action -->
        <menuitem id="courses_menu" name="Courses" parent="openacademy_menu"
                  action="course_list_action"/>
        <!-- Full id location:
             action="openacademy.course_list_action"
             It is not required when it is the same module -->
    </data>
</openerp>
```


# 基本视图
```视图定义一个模型的显示记录方式。每种类型的视图代表了一种可视化模块(记录的列表,图的聚合,…)。视图一般可以要求通过他们的类型(如合作列表)或具体的渠道用他们的id来请求，对于一般的视图使用正确的类型和最低优先级(所以每种优先级视图就是那种类型的默认视图)。视图继承允许改变在其他地方声明过的视图 (添加或删除内容)。```


## 通用视图声明
```视图是声明为模型ir.ui.view的记录，视图所隐含的类型是根元素的字段arch暗示```
```python
<record model="ir.ui.view" id="view_id">
    <field name="name">view.name</field>
    <field name="model">object_name</field>
    <field name="priority" eval="16"/>
    <field name="arch" type="xml">
        <!-- view content: <form>, <tree>, <graph>, ... -->
    </field>
</record>
```

**危险**

```XML视图的内容。
因此arch 字段必须声明为”type="xml"才能被正确解```

## 树视图
```树视图,也称为列表视图,显示记录在表格里面。他们的根元素是<tree>.。树视图的简单的形式就是在表格中简单地列出所有要显示的字段(每个字段作为一个列):```
```python
<tree string="Idea list">
    <field name="name"/>
    <field name="inventor_id"/>
</tree>
```


## 表格视图
```表单是用于创建和编辑单个记录。他们的根元素是<form>.。他们由高层结构元素组成(groups, notebooks))和交互元素(buttons and fields)):```
```python
<form string="Idea form">
    <group colspan="4">
        <group colspan="2" col="2">
            <separator string="General stuff" colspan="2"/>
            <field name="name"/>
            <field name="inventor_id"/>
        </group>

        <group colspan="2" col="2">
            <separator string="Dates" colspan="2"/>
            <field name="active"/>
            <field name="invent_date" readonly="1"/>
        </group>

        <notebook colspan="4">
            <page string="Description">
                <field name="description" nolabel="1"/>
            </page>
        </notebook>

        <field name="state"/>
    </group>
</form>
```

>练习
```使用XML定制表单视图
创建自己的课程对象的形式视图。数据应该是:显示的名称和课程描述```

**openacademy/views/openacademy.xml**
```python
<?xml version="1.0" encoding="UTF-8"?>
<openerp>
    <data>
        <record model="ir.ui.view" id="course_form_view">
            <field name="name">course.form</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <form string="Course Form">
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="description"/>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>

        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
```

**练习**
```Notebooks
Course表格视图中，把discription字段放到标签下，方便后面增加其他的标签来包含额外的信息
修改课程形式视图如下:```

**openacademy/views/openacademy.xml**
```python
                    <sheet>
                        <group>
                            <field name="name"/>
                        </group>
                        <notebook>
                            <page string="Description">
                                <field name="description"/>
                            </page>
                            <page string="About">
                                This is an example of notebooks
                            </page>
                        </notebook>
                    </sheet>
                </form>
            </field>
```

为了使布局更为灵活视图也可以使用普通的HTML

```python
<form string="Idea Form">
    <header>
        <button string="Confirm" type="object" name="action_confirm"
                states="draft" class="oe_highlight" />
        <button string="Mark as done" type="object" name="action_done"
                states="confirmed" class="oe_highlight"/>
        <button string="Reset to draft" type="object" name="action_draft"
                states="confirmed,done" />
        <field name="state" widget="statusbar"/>
    </header>
    <sheet>
        <div class="oe_title">
            <label for="name" class="oe_edit_only" string="Idea Name" />
            <h1><field name="name" /></h1>
        </div>
        <separator string="General" colspan="2" />
        <group colspan="2" col="2">
            <field name="description" placeholder="Idea description..." />
        </group>
    </sheet>
</form>
```

## 搜索视图

定制相关的搜索字段列表视图(或者其他聚合视图)。他们的根元素<search>的组成字段决定了那些字段能够被搜索

```python
<search>
    <field name="name"/>
    <field name="inventor_id"/>
</search>
```

```如果没有搜的视图模块存在,Odoo就会自动生成一个，但是只允许用name来搜索的字段。```


>练习
搜索课程用标题或者表述搜索课程

**openacademy/views/openacademy.xml**
```python
            </field>
        </record>

        <record model="ir.ui.view" id="course_search_view">
            <field name="name">course.search</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <search>
                    <field name="name"/>
                    <field name="description"/>
                </search>
            </field>
        </record>

        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
```

# 模型之间的关系
``一个模型的记录可能和另一个模型的记录相关联，例如, 销售记录和包含客户数据的客户记录相关连，也和销售订单记录关联``


>练习
创建一个学期模型Open Academy模块,我们考虑一个会话模型:一个学期是一个课程在给定的时间内针对给定的读者发生的事件。会话有一个名称、开始日期、持续时间和在座位的数量。添加一个动作和一个菜单项来显示它们。在菜单项里面可以看到新模型
1.在openacademy / models.py中创建会话类。
2 在openacademy/view/openacademy.xml中添使用会话对象的加权限.添加访问会话对象。

**openacademy/models.py**
```python
    name = fields.Char(string="Title", required=True)
    description = fields.Text()


class Session(models.Model):
    _name = 'openacademy.session'

    name = fields.Char(required=True)
    start_date = fields.Date()
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")

**openacademy/views/openacademy.xml**
```python
        <!-- Full id location:
             action="openacademy.course_list_action"
             It is not required when it is the same module -->

        <!-- session form view -->
        <record model="ir.ui.view" id="session_form_view">
            <field name="name">session.form</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <form string="Session Form">
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="start_date"/>
                            <field name="duration"/>
                            <field name="seats"/>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
                  parent="openacademy_menu"
                  action="session_list_action"/>
    </data>
</openerp>
```



**请注意**
```digits=(6, 2)指定一个浮点数的精度数：6是位数的总数, 而2是逗号后位数的数量。注意,它决定逗号前的数字位数最多4```


## 关系字段
```关系字段链接记录,同样的模型(层次)或不同的模型。关系字段类型是:
Many2one(other_model, ondelete='set null')
A simple link to an other object:
print foo.other_id.name
See also
foreign keys
One2many(other_model, related_field)```

一个虚拟关系,相反的Many2one和One2many充当一个容器的记录、访问它导致一组(可能为空)的记录:
```python
for other in foo.other_ids:
    print other.name
```

**危险**

```因为One2many是一个虚拟的关系,必须有一个Many2one字段在其他模块中,其名称必须是related_field```



```Many2many(其他模块)
双向的多重关系,任何记录可以与任意数量的另外一边的记录相关联。表现的形式得像一个容器的记录,访问结果也可能是空的记录```



>练习
Many2one关系
使用many2one，修改课程和会话模型来反映他们和其他模型的关系
每一个课程有一个负责的用户,这个字段的值是一个内置模型res.users的记录。
一个会话有一个指导,这个字段的值是一个内置的模型res.partner的记录。
会话相关的课程,该字段的值是openacademy记录模型，而且这个值必须要有
适配视图。
1.添加有关的Many2one字段到模型并且
2.添加他们的到视图里面

**openacademy/models.py**
```python
    name = fields.Char(string="Title", required=True)
    description = fields.Text()

    responsible_id = fields.Many2one('res.users',
        ondelete='set null', string="Responsible", index=True)


class Session(models.Model):
    _name = 'openacademy.session'
    start_date = fields.Date()
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor")
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
```
**openacademy/views/openacademy.xml**
```python
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="responsible_id"/>
                        </group>
                        <notebook>
                            <page string="Description">
            </field>
        </record>

        <!-- override the automatically generated list view for courses -->
        <record model="ir.ui.view" id="course_tree_view">
            <field name="name">course.tree</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <tree string="Course Tree">
                    <field name="name"/>
                    <field name="responsible_id"/>
                </tree>
            </field>
        </record>

        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
                <form string="Session Form">
                    <sheet>
                        <group>
                            <group string="General">
                                <field name="course_id"/>
                                <field name="name"/>
                                <field name="instructor_id"/>
                            </group>
                            <group string="Schedule">
                                <field name="start_date"/>
                                <field name="duration"/>
                                <field name="seats"/>
                            </group>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>

        <!-- session tree/list view -->
        <record model="ir.ui.view" id="session_tree_view">
            <field name="name">session.tree</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <tree string="Session Tree">
                    <field name="name"/>
                    <field name="course_id"/>
                </tree>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
```

>练习
逆one2many关系
使用逆one2many关系字段,修改模型以反映课程和课程之间的关系。
1.修改Course类
2. 在表单视图中添加字段。

**openacademy/models.py**
```python
    responsible_id = fields.Many2one('res.users',
        ondelete='set null', string="Responsible", index=True)
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sessions")


class Session(models.Model):
openacademy/views/openacademy.xml
                            <page string="Description">
                                <field name="description"/>
                            </page>
                            <page string="Sessions">
                                <field name="session_ids">
                                    <tree string="Registered sessions">
                                        <field name="name"/>
                                        <field name="instructor_id"/>
                                    </tree>
                                </field>
                            </page>
                        </notebook>
                    </sheet>
```


>多重的的many2many关系
使用many2many关系字段，修改会话模块去设置每个会话的参与者，与会者将通过合作伙伴的记录为代表，所以我们将res.partner的模块内置以适应相应的视图。
1修改会话类，
2在窗体视图中添加字段。

**openacademy/models.py**
```python
    instructor_id = fields.Many2one('res.partner', string="Instructor")
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
openacademy/views/openacademy.xml
                                <field name="seats"/>
                            </group>
                        </group>
                        <label for="attendee_ids"/>
                        <field name="attendee_ids"/>
                    </sheet>
                </form>
            </field>
```

# 模型继承
```在现在的模块化方式里面Odoo提供了两种继承机制来扩展现有的模型
第一个继承机制允许一个模块修改另一个模块中定义的操作
将字段添加到模型中,
在模型中重写定义的字段
向模型添加约束
添加一个模型的方法,
重写现有的方法模型。
第二个继承机制(授权)允许链接模型的每个记录以便记录在父母模型中,并提供了透明的方式访问父记录的字段。```



## 视图继承
```替代修改现有的视图(重写),Odoo提供了子“扩展”视图应用于根视图的视图继承,可以通过父视图进行添加和删除扩展视图引用其父视图使用inherit_id字段,取代单一视图的arch字段而由若干的xpath元素选择和改变他们的父视图的内容组成```
```python
<!-- improved idea categories list -->
<record id="idea_category_list2" model="ir.ui.view">
    <field name="name">id.category.list2</field>
    <field name="model">idea.category</field>
    <field name="inherit_id" ref="id_category_list"/>
    <field name="arch" type="xml">
        <!-- find field description and add the field
             idea_ids after it -->
        <xpath expr="//field[@name='description']" position="after">
          <field name="idea_ids" string="Number of ideas"/>
        </xpath>
    </field>
</record>
```


```表达式
XPath表达式选择单个元素在父视图。如果没有匹配或匹配了多个对象，就会报错
内部
查找到匹配的元素
替换
在匹配的元素后面添加XPath的枝干部分替换添加Xpath的body最后匹配的元素取代替换匹配元素的Xpath的body
之前
在匹配的元素之前插入Xpath的body作为兄弟节点
后
插入Xpath的body作为一个匹配的元素后,兄弟节点
属性
在Xpath的body里面改变的匹配元素的属性使用attribute元素```


**提示**
```匹配单个元素的时候position属性可以直接在元素被查找到的时候设置。下面两种继承将相同的结果。```
```python
<xpath expr="//field[@name='description']" position="after">
    <field name="idea_ids" />
</xpath>

<field name="description" position="after">
    <field name="idea_ids" />
</field>
```

>练习
改变现有内容
使用模块继承,修改现有的合作伙伴模块添加一个boolean类型的instructor字段,many2many字段对应session-partner关系使用视图继承
显示这个领域合作伙伴的表格继承


**请注意**
```可以检查开发者模块视图,找其外部ID并在该位置放置一个新的字段
1.创建一个openacademy/partner.py文件
1. 2.并将它导入到_openerp__.py中```

```python
2. # -*- coding: utf-8 -*-
3. from . import controllers
4. from . import models
5. from . import partner
6. openacademy/__openerp__.py
7.         # 'security/ir.model.access.csv',
8.         'templates.xml',
9.         'views/openacademy.xml',
10.         'views/partner.xml',
11.     ],
12.     # only loaded in demonstration mode
13.     'demo': [
14. openacademy/partner.py
15. # -*- coding: utf-8 -*-
16. from openerp import fields, models
17.
18. class Partner(models.Model):
19.     _inherit = 'res.partner'
20.
21.     # Add a new column to the res.partner model, by default partners are not
22.     # instructors
23.     instructor = fields.Boolean("Instructor", default=False)
24.
25.     session_ids = fields.Many2many('openacademy.session',
26.         string="Attended Sessions", readonly=True)
```
**openacademy/views/partner.xml**
```python
28. <?xml version="1.0" encoding="UTF-8"?>
29.  <openerp>
30.     <data>
31.         <!-- Add instructor field to existing view -->
32.         <record model="ir.ui.view" id="partner_instructor_form_view">
33.             <field name="name">partner.instructor</field>
34.             <field name="model">res.partner</field>
35.             <field name="inherit_id" ref="base.view_partner_form"/>
36.             <field name="arch" type="xml">
37.                 <notebook position="inside">
38.                     <page string="Sessions">
39.                         <group>
40.                             <field name="instructor"/>
41.                             <field name="session_ids"/>
42.                         </group>
43.                     </page>
44.                 </notebook>
45.             </field>
46.         </record>
47.
48.         <record model="ir.actions.act_window" id="contact_list_action">
49.             <field name="name">Contacts</field>
50.             <field name="res_model">res.partner</field>
51.             <field name="view_mode">tree,form</field>
52.         </record>
53.         <menuitem id="configuration_menu" name="Configuration"
54.                   parent="main_openacademy_menu"/>
55.         <menuitem id="contact_menu" name="Contacts"
56.                   parent="configuration_menu"
57.                   action="contact_list_action"/>
58.     </data>
59. </openerp>
```

## 域
在Odoo中,域是编码条件的记录值。一个域是一个用于选择一个模块记录的一个子集列表的标准。标准是三个字段名称,操作符和一个值。例如,当用于以下领域产品模型选择所有单价超过1000的服务
```python
[('product_type', '=', 'service'), ('unit_price', '>', 1000)]
```


```默认标准是结合一个隐含的AND。逻辑运算符$(和),|(或)和!(非)可用做显式的结合标准。它们用于前缀位置(操作他的人插入到参数之前而不是参数之间)。例如选择一个产品“服务于OR单价不是在1000年和2000年之间产品的服务”:```
```python
['|',
    ('product_type', '=', 'service'),
    '!', '&',
        ('unit_price', '>=', 1000),
        ('unit_price', '<', 2000)]
```


```一个域参数参数可以添加相关的字段去限制有效记录，对此你可以在客户端接口里面去选择有关系的记录```


>练习
域相关的字段当为一个会话选择指导,只有教师(与instructors匹配要设置为True)应清晰可见。

**openacademy/models.py**
```pyt
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=[('instructor', '=', True)])
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
```

**注意**
```在服务端声明为字符列表的域用服务器来评估不能引用右边的动态值，在客户端声明为字符串的域可以把字段设在右边```


>练习
更复杂的领域
创建新伙伴类别Teacher / Level 1 and Teacher / Level 2个会话的指导员可以是一个指导员或教师（任何级别的）。
1.修改会话模块的域
2.修改会话模型的域修改openacademy/view/partner.xml xml来获得toPartner类别:

**openacademy/models.py**
```python
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=['|', ('instructor', '=', True),
                     ('category_id.name', 'ilike', "Teacher")])
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
    ```
**openacademy/views/partner.xml**
```python
        <menuitem id="contact_menu" name="Contacts"
                  parent="configuration_menu"
                  action="contact_list_action"/>

        <record model="ir.actions.act_window" id="contact_cat_list_action">
            <field name="name">Contact Tags</field>
            <field name="res_model">res.partner.category</field>
            <field name="view_mode">tree,form</field>
        </record>
        <menuitem id="contact_cat_menu" name="Contact Tags"
                  parent="configuration_menu"
                  action="contact_cat_list_action"/>

        <record model="res.partner.category" id="teacher1">
            <field name="name">Teacher / Level 1</field>
        </record>
        <record model="res.partner.category" id="teacher2">
            <field name="name">Teacher / Level 2</field>
        </record>
    </data>
</openerp>
```

# 计算字段和默认值
```到目前为止，字段已直接存储在数据库中，并直接从数据库中检索到。也可以计算字段。在这种情况下，字段的值不是从数据库中检索出来的，而是通过调用模型的方法来计算出来的。
创建计算字段，创建一个领域，并设置compute为属性方法名。计算方法应该在self中简单地设置字段的值来计算每个记录```


**危险**
```self是一种集合
self对象是一个记录集，即有序的记录集合。它支持标准的Python操作集合，像Len（self）和iter(self)，加上像recs1 + recs2额外的设置操作。
重复self一个一个的记录，每个记录本身是一个大小为1的集合。您可以访问指定使用点符号的单个记录的字段通过是用.标记，例如record.name。```
```Python
import random
from openerp import models, fields, api

class ComputedModel(models.Model):
    _name = 'test.computed'

    name = fields.Char(compute='_compute_name')

    @api.multi
    def _compute_name(self):
        for record in self:
            record.name = str(random.randint(1, 1e6))

```


# 依赖
```计算字段的值通常取决于计算记录上的其他字段的值。ORM（对象关系映射，英语：Object Relation Mapping，简称ORM）认为开发商在计算方法里面用装饰器depends()指定那些依赖关系。当其依赖被修改给定的依赖关系被ORM用来触发重新计算一些字段```
```python
from openerp import models, fields, api

class ComputedModel(models.Model):
    _name = 'test.computed'

    name = fields.Char(compute='_compute_name')
    value = fields.Integer()

    @api.depends('value')
    def _compute_name(self):
        for record in self:
            record.name = "Record with value %s" % record.value

```

>练习
计算字段
将所占的比例添加到会话模型
在树视图和表格视图中显示该字段
显示字段作为一个进度条
1添加一个计算字段到会话
2、在会话视图中显示字段：

**openacademy/models.py**
```python
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    taken_seats = fields.Float(string="Taken seats", compute='_taken_seats')

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
            if not r.seats:
                r.taken_seats = 0.0
            else:
                r.taken_seats = 100.0 * len(r.attendee_ids) / r.seats
```
**openacademy/views/openacademy.xml**
```python
                                <field name="start_date"/>
                                <field name="duration"/>
                                <field name="seats"/>
                                <field name="taken_seats" widget="progressbar"/>
                            </group>
                        </group>
                        <label for="attendee_ids"/>
                <tree string="Session Tree">
                    <field name="name"/>
                    <field name="course_id"/>
                    <field name="taken_seats" widget="progressbar"/>
                </tree>
            </field>
        </record>

```

## 默认值
```任何字段都可以给定一个默认值。字段定义中，添加选项default=X，X也是一个Python文本值（可以是布尔，整型，浮点型，字符串），或用一个函数来获取记录集并返回其值：```
```Python
name = fields.Char(default="Unknown")
user_id = fields.Many2one('res.users', default=lambda self: self.env.user)
```

**注意**
```对象self.env可以访问请求参数和其他需要的东西：
•self.env.cr或self._cr是数据库指针对象；它是用来查询数据库
•self.env.uid或self._uid是当前用户的数据库ID
•self.env.user是当前用户的记录
•self.env.context或self._context是文本的文件夹
•elf.env.ref(xml_id)返回一个对应XML ID记录
•self.env[model_name] [返回给定模块的一个实例```


>练习
活动对象的默认值
•定义start_date为今天的默认值为（视具体的日期而定）。
•在会话类中添加一个active字段，并设置该会话为默会话。

**openacademy/models.py**
```python
    _name = 'openacademy.session'

    name = fields.Char(required=True)
    start_date = fields.Date(default=fields.Date.today)
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")
    active = fields.Boolean(default=True)

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=['|', ('instructor', '=', True),
```
**openacademy/views/openacademy.xml**
```python
                                <field name="course_id"/>
                                <field name="name"/>
                                <field name="instructor_id"/>
                                <field name="active"/>
                            </group>
                            <group string="Schedule">
                                <field name="start_date"/>
```


**注意**
Odoo有内置的规则规定active字段设置为false表示不可见

# Onchange
 ```“onchange”的机制为客户端界面更新表单提供了一种方式，用户为字段赋值时，这些数据不保存到数据库。例如，假设一个模块有三个字段amount, unit_price和price，在其他字段被被修改时你想更新表单上的价格。为了实现这一目标，定义一个方法，他的self在表单里面所代表记录，并且在该字段已经触发的时候用onchange()来指定装饰。在self上做的任何改变都会反映在表单里面```
```python
<!-- content of form view -->
<field name="amount"/>
<field name="unit_price"/>
<field name="price" readonly="1"/>
# onchange handler
@api.onchange('amount', 'unit_price')
def _onchange_price(self):
    # set auto-changing field
    self.price = self.amount * self.unit_price
    # Can optionally return a warning and domains
    return {
        'warning': {域
            'title': "Something bad happened",
            'message': "It was very bad indeed",
        }
    }
```

```对于计算字段，onchange的值表现形式是固定的，通过播放表单可以达到最大限度的可见性，改变位置或参与者的数量，taken_seats进度条会自动更新。```



>练习
警告
添加一个明确的onchange警告无效的值，例如，位置设置为负数，或者参与者比位置还多。

**openacademy/models.py**
```python
                r.taken_seats = 0.0
            else:
                r.taken_seats = 100.0 * len(r.attendee_ids) / r.seats

    @api.onchange('seats', 'attendee_ids')
    def _verify_valid_seats(self):
        if self.seats < 0:
            return {
                'warning': {
                    'title': "Incorrect 'seats' value",
                    'message': "The number of available seats may not be negative",
                },
            }
        if self.seats < len(self.attendee_ids):
            return {
                'warning': {
                    'title': "Too many attendees",
                    'message': "Increase seats or remove excess attendees",
                },
            }
```
