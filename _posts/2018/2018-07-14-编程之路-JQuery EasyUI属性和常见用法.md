---
layout:     post             				# 使用的布局（不需要改）
title:      JQuery EasyUI属性和常见用法  # 标题 
subtitle:    					  				#副标题
date:       2018-07-14  					# 时间
author:     Ian                  			# 作者
header-img: img/ian-bg-red.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 前端
---
> 转载sidecore https://www.cnblogs.com/sidecore/p/3199688.html
> 

## EasyUI属性 
属性分为CSS片段和JS片段。

## CSS类定义：

1. div easyui-window        生成一个window窗口样式。

```txt
      属性如下：
                   1)modal:是否生成模态窗口。true[是] false[否]
                   2)shadow:是否显示窗口阴影。true[显示] false[不显示]
```
      
2. div easyui-panel           生成一个面板。
```txt
       属性如下:
                 1)title:该标题文本显示在面板头部。
                 2)iconCls:在面板上通过一个CSS类显示16x16图标。
                 3)width:设置面板宽度。默认auto。
                 4)height:设置面板高度。默认auto。
                 5)left:设置面板左边距。
                 6)top:设置面板顶部位置。
                 7)cls:在面板中增加一个Class类。
                 8)headerCls:在面板头部中增加一个Class类。
                 9)bodyCls:在面板内容中增加一个Class类。
                10)style:在面板中增加一个指定样式。
                11)fit:当True时设置该面板尺寸适合于它的父容器。默认false。
                12)border:当定义时显示面板边界。默认true。
                13)doSize:如果设置为True，该面板将重绘大小，并重建布局。默认true。
                14)collapsible:当定义时显示可折叠面板的按钮。默认false。
                15)minimizable:当定义时显示最小化面板的按钮。默认false。
                16)maximizable:当定义时显示最大化面板的按钮。默认false。
                17)closable:当定义时显示关闭面板的按钮。默认false。
                18)tools:自定义工具栏，每个工具都包含两个属性:iconCls、handler。
                19)collapsed:当定义时该面板初始化时处于收缩状态。默认false。
                20)minimized:当定义时该面板初始化时处于最小化状态。默认false。
                21)maximized:当定义时该面板初始化时处于最大化状态。默认false。
                22)closed：当定义时该面板初始化时处于关闭状态。默认false。
                23)href:一个url，加载远程数据并显示在面板中。
                24)loadingMessage:当加载远程数据时，在面板中显示一个消息。默认Loading…
             事件如下:
                 1)onLoad:当远程数据加载完毕后激活。
                 2)onBeforeOpen:当面板打开前激活。
                 3)onOpen:当面板打开后激活。
                 4)onBeforeClose:当面板关闭前激活。
                 5)onClose:当面板关闭后激活。
                 6)onBeforeDestroy:当面板销毁前激活。
                 7)onDestroy:当面板销毁后激活。
                 8)onBeforeCollpase:当面板收缩前激活。
                 9)onCollapse:当面板收缩后激活。
                10)onBeforeExpand:当面板扩展前激活。
                11)onExpand:当面板扩展后激活。
                12)onResize:当面板重绘后激活。
                      width:新建的外部宽度
                      height:新建的外部高度
                13)onMove:当面板移动后激活。
                     left:左侧新位置。
                     top:顶部新位置。
                14)onMaximize:当窗口最大化后激活。
                15)onRestore:当窗口恢复到原来大小时激活。
                16)onMinimize:当窗口最小化后激活。
             方法如下：
                 1)options:返回options属性。
                 2)panel:返回面板对象。
                 3)header:返回面板头部对象。
                 4)body:返回面板主体对象。
                 5)setTitle:设置头部的标题文本。
                 6)open:当forceOpen参数设置为true时，面板打开时绕过onBeforeOpen回调函数。
                 7)close:当forceClose参数设置为true时，该面板关闭时绕过onBeforeClose回调函数。
                 8)destroy:当forceDestroy参数设置为true时该面板销毁时绕过onBeforeDestroy回调函数。
                 9)refresh:当href属性设置后刷新该面板以加载远程数据。
                10)resize:设置面板的大小和布局。该options对象包含以下属性:
                     width：新的面板宽度。
                     height：新的面板高度。
                     left：新的面板左侧位置。
                     top：新的面板顶部位置。
                11)move:移动面板到一个新的位置。该options对象包含以下属性：
                     left：新的面板左侧位置。
                     top：新的面板顶部位置。
```

3. a  easyui-linkbutton                    生成链接类型的按钮。

```txt
       属性如下：
            1)disabled:当True时禁用该按钮。默认false。
            2)plain:当True时显示一个普通效果。默认false。
```

4. input/textarea easyui-validatebox       生成字段验证。

```txt
              属性如下：
              1)required:true[必需] false[不必需] 默认false
              2)validType:
                 a、length[a,b] 字段长度控制在a至b之间。
                 b、email       验证Email。
                 c、url      验证网络地址。
              3)missingMessage：当文本时出现空时弹出该工具提示，系统有默认[英文]，自定义可覆盖它。
              4)invalidMessage：当文本内容无效后弹出该工具提示，系统有默认[英文]，自定义可覆盖它。
```

5. ul easyui-tree         生成一个树形结构。

```txt
             属性如下：
              1)url:一个获取远程数据的地址。
              2)animate:当展开或折叠节点时是否定义动画效果。true[是] false[否] 默认false
             节点属性如下：
             1)text:节点的显示文本。
             2)id:节点ID，对于加载远程数据时非常重要。
             3)state:节点状态，'open'或'closed'，默认为'open'。当设置为'关闭'，该节点包含子节点，并将远程站点加载它们(并非触发再加载)。
             4)attributes:为节点添加自定义属性。
             5)children:以数组节点的方式定义一些字节点。
             事件如下：
                 1)onClick:
                    当用户点击一个节点时激活，该节点参数包含如下属性：
                    id：节点ID
                    text:节点文本
                    attributes:节点自定义属性。
                    target:目标点击的DOM对象。
              2)onLoadSuccess:
                   当数据成功加载数据时激活，该参数跟jQuery.ajax的'success'函数效果相同。
              3)onLoadError:
                  当数据加载数据失败时激活，该参数跟jQuery.ajax的'error'函数效果相同。
             方法如下:
                 1)reload:重新加载树数据。
                 2)getSelected:获取选中的节点并返回它，如果没有选择节点将返回null。
                 3)collapse:折叠一个节点，该目标参数是该节点的DOM对象。
              4)expand:展开一个节点，该目标参数是该节点的DOM对象。 
              5)append:在一个父节点追加一些子节点。
                    param有两个属性：
                    parent:DOM对象，把它作为父节点追加(它们)。
                    data:array，或者节点数据。
              6)remove:删除它以及它以下的子节点，该目标参数是该节点的DOM对象。 
```

6. table easyui-datagrid                   生成一个表格。

```txt
             属性如下：
                 1)title:该DataGrid面板的标题文本。
                 2)iconCls:一个CSS类，将提供一个背景图片作为标题图标。
                 3)border：当true时，显示该datagrid面板的边框。
                 4)width:面板宽度，自动列宽。
                 5)height:面板高度，自动列高。
                 6)columns:该DataGrid列配置对象，查看column属性可获取更多信息。
                 7)frozenColumns:跟Columns属性相同，但是这些列将会被固定在左边。
                 8)striped:当true时，单元格显示条纹。默认false。
                 9)method:通过该方法类型请求远程数据。默认post。
                10)nowrap:当true时，显示数据在同一行上。默认true。
                11)idField:说明哪个字段是一个标识字段。
                12)url:一个URL，从远程站点获取数据。
                13)loadMsg:当从远程站点加载数据时，显示一个提示信息。默认"Processing,please wait …"。自定义覆盖。
                14)pagination:当true时在DataGrid底部显示一个分页工具栏。默认false。
                15)rownumbers:当true时显示行号。默认false。
                16)singleSelect:当true时只允许当前选择一行。默认false。
                17)fit:当true时，设置大小以适应它的父容器。默认false。
                18)pageNumber:当设置分页属性时，初始化的页码编号。默认从1开始
                19)pageSize:当设置分页属性是，初始化的页面大小。默认10行
                20)pageList:当设置分页属性时，初始化页面的大小选择清单。默认[10,20,30,40,50]
                21)queryParams:当请求远程数据时，也可以发送额外的参数。
                22)sortName:定义哪列可以排序。
                23)sortOrder:定义列的排列顺序，只能是'asc'或'desc'。默认asc。
             Column属性如下：
                 1)title:该列标题文本。
                 2)field:该列对应的字段名称。
                 3)width：列宽。
                 4)rowspan:说明该单元格需要多少行数。
                 5)colspan:说明该单元格需要多少列数。
                 6)align:说明Column数据的对齐方式。'left'，'right'，'center' 都可以使用。
                 7)sortable:当true时，允许该列进行排序。
                 8)checkbox:当true时，允许该列出现checkbox。
             事件如下：
                 1)onLoadSuccess:当远程数据加载成功是激活。
                 2)onLoadError:当远程数据加载发现一些错误时激活。
                 3)onClickRow:当用户点击某行时激活，参数包含：
                    rowIndex: 点击的行索引，从0开始。
                    rowData: 点击行时对应的记录。
                4)onDblClickRow:当用户双击某行时激活，参数包含：
                    rowIndex: 点击的行索引，从0开始。
                    rowData: 点击行时对应的记录。
                5)onSortColumn:当用户对某列排序时激活，参数包含：
                   sort:排序字段名称。
                   order:排序字段类型。
                6)onSelect:当用户选择某行时激活，参数包含:
                   rowIndex: 点击的行索引，从0开始。
                   rowData: 点击行时对应的记录。
                7)onUnselect:当用户取消选择某行时激活，参数包含:
                    rowIndex: 点击的行索引，从0开始。
                    rowData: 点击行时对应的记录。
             方法如下：
                 1)options:返回选择对象。
                 2)resize:重调大小，生成布局。
                 3)reload:重新加载数据。
                 4)fixColumnSize:固定列大小。
                 5)loadData:加载本地数据，过去的行会被删除。
                 6)getSelected:返回第一个选中行的记录，若未选返回null。
                 7)getSelections:返回选中的所有行，当没有选择记录时将返回空数组。
                 8)clearSelections:清除所有选项的选择状态。
                 9)selectRow:选择一行，行索引从0开始。
                10)selectRecord:通过传递一个ID值参数，选择一行。
                11)unselectRow:取消选择一行。
```

7. div easyui-tabs                         生成一个tab容器。

```txt
             属性如下：
                 1)width:容器宽度，自动列宽。
                 2)height:容器高度，自动列高。
                 3)idSeed:该根id衍生成标签面板DOM id属性。默认0
                 4)plain：当true时，该Tab渲染不使用容器背景图片。默认false
                 5)fit:当true时，设置该Tab大小以适应它的父容器。默认false
                 6)border:当true时，显示该Tab边框。
                 7)scrollIncrement:
                 8)scrollDuration:
             事件如下：
                 1)onLoad:当一个ajax Tab面板需要加载远程数据时激活。该参数跟jQuery.ajax的'success'函数效果相同。
                 2)onSelect:当用户选择一个Tab面板时激活。
                 3)onClose:当用户关闭一个Tab面板时激活。
             方法如下:
                 1)resize:重绘该Tab容器的布局。
                 2)add:新增加一个Tab面板，该选项参数是一个配置对象，看Tab面板属性可获取更多信息。
                 3)close：关闭该Tab面板，标题参数显示你要关闭的对象。
                 4)select:选择一个Tab面板。
                 5)exists:如果该Tab面板存在即显示。
             Tab面板属性如下：
                 1)id:该Tab面板DOM id属性。
                 2)text:该Tab面板标题文本。
                 3)content:该Tab面板内容。
                 4)href:一个URL，加载远程内容以填充Tab面板。
                 5)cache:当true时，缓存Tab面板，当href 属性设置后有效。默认true
                 6)icon:增加一个CSS class图标以显示在Tab面板的标题旁。
                 7)closable:当true时，该Tab面板将显示可关闭按钮，点击能关闭该Tab面板。默认false
                 8)selected:当true时，该Tab面板将被选中。默认false
                 9)width:面板宽度，自动列宽。
                10)height:面板高度，自动列高。
```         
8. div menu-sep              生成一个菜单分隔线。

9. a easyui-splitbutton         生成一个菜单列。

10. div easyui-accordion        生成手风琴式下拉框。继承自panel

11. select easyui-combobox       生成一个组合下拉框。

```txt
             属性如下：
                 1)width:容器宽度，自动列宽。
                 2)listWidth:该组合下拉框的宽度。
                 3)listHeight:该组合下拉框的高度。
                 4)valueField：把该基础数据的值名称绑定到组合下拉框中[value]。
                 5)textField:把该基础数据的字段名称绑定到组合下拉框中[text]。
                 6)editable:当True时，可直接在文字域中键入文本。默认true。
                 7)url:一个URL，从远程加载列表数据。
             事件如下：
                 1)onLoadSuccess:当远程数据加载成功是激活。
                 2)onLoadError:当远程数据加载发现一些错误时激活。
                 2)onSelect:当用户选择一个列表选项时激活。
                 3)onChange:当该字段的值发生改变时激活。
             方法如下:
                 1)select: 在下拉列表中选择一个值。
                 2)setValue: 设置指定值到该字段。在'param' 参数可以是一个字符串或者一个JS对象。注:JS对象包含的属性对应valueField和TextField两个属性。
                 3)getValue: 获取该字段的值。
                 4)reload:   重新请求远程列表数据。
```

12. select easyui-combotree      生成一个组合树形框。

```txt
            属性如下：
            1)width:容器宽度，自动列宽。
            2)treeWidth:该树形下拉框的宽度。
            3)treeHeight:该树形下拉框的高度。
            4)url:一个URL，从远程加载树形数据。
             事件如下：
             1)onSelect:当用户选择一个树形节点时激活。
             2)onChange:当该字段的值发生改变时激活。
             方法如下:
             1)setValue: 设置指定值到该字段。在'param' 参数可以是一个树形节点ID值或者一个JS对象。注:JS对象包含的属性对应id和text两个属性。
             2)getValue: 获取该字段的值。 
             3)reload:   重新请求远程列表数据。
```

13. body[div] easyui-layout      生成一个布局。


```txt
            属性如下：
            1)title:该面板标题文本。
            2)region:定义布局面板的位置，包含下列值:north,south, east, west, center。
            3)border:当True时显示布局面板的边框。默认为True。
            4)split: 当True时显示一个分割符以使用户改变面板的尺寸。默认false。
            5)icon:一个图标CSS类，在面板头部显示一个图标。 
            6)href:一个URL,以从远程站点加载数据。             
```    
14. div easyui-menu        生成一个菜单。
```txt
            属性如下：
            1)zIndex: Menu z-index样式。注释：z-index 属性设置元素的堆叠顺序。 
            2)left:菜单左起位置。默认0。
            3)top: 菜单顶部位置。默认0。
            4)href:当点击菜单项时能在当前浏览器窗口显示不同的网址。
            事件如下:
            1)onShow:激活后显示菜单。
            2)onHide:激活后隐藏菜单。
            方法如下:
            1)show:在指定的位置显示一个菜单。该位置上包含两个参数：
                left:新的左起位置。
               top:新的顶部位置。
            2)hide:隐藏一个菜单。
```   
15. a easyui-menubutton       生成一个菜单按钮。
```txt
            属性如下：
            1)disabled:当True时禁用该按钮。默认false。
            2)plain:当True时显示一个普通效果。默认false。
            3)menu:一个选择器名称，用来创建相应的菜单。
            4)duration: 当悬停该按钮时，定义菜单的持续显示时间，单位为毫秒。默认100。
```
16. input easyui-numberbox      生成一个数字输入框。
```txt
            选项如下:
            1)min:允许的最小值。当输入值小于最小值时，显示最小值。
            2)max:允许的最大值。当输入值大于最大值时，显示最大值。
            3)precision:分隔符后能精确的小数点位数。整数默认会追加小数点位数。 
```               

## JS定义：
1. .window            生成一个window窗口。

2. .tree                  生成一个树形结构。

3. .datagrid           生成一个表格。

4. .combobox        生成一个组合下拉框。

5. .combotree       生成一个组合树形框。

6. .dialog               生成一个对话框。它继承自window
```txt
      私有属性如下：
                 1)title:该对话框标题文本。默认"New Dialog"。
                 2)collapsible:当True时可显示折叠按钮。默认false。
                 3)minimizable:当True时可显示最小化按钮。默认false。
                 4)maximizable:当True时可显示最大化按钮。默认false。
                 5)resizable:当True时能重绘对话框大小。默认false。
                 6)toolbar:该工具栏置于对话框的顶部，每个工具栏包含:text, iconCls, disabled, handler等属性。
                 7)buttons:这个按钮置于对话框的底部，每个按钮包含:text, iconCls, handler等属性。
```      
7. .draggable          生成一个可自由拖动的块。
```txt
      属性如下:
              1)handle:选择"#id"进行拖动。
              2)disabled:当True时停止自由拖动。默认false。
              3)edge:开始拖动拖动块时的宽度。默认0。
              4)axis:当拖动块移动时定义轴，可选值是'v'或者'h',当超出'v'和'h'的方位时将设置为null。
     事件如下：
                 1)onStartDrag:当目标对象开始拖动时激活。
                 2)onDrag:在拖动期间激活。返回false将不会实际拖动它(的位置)。
                 3)onStopDrag:当目标对象停止拖动时激活。
```
8. .linkbutton          生成一个链式按钮。

9. .messager           生成一个消息框。
```txt
             选项如下：
                 1)ok:显示确定按钮文本。
                 2)cancel:显示取消按钮文本。 
             方法如下:
             1)show:在屏幕的右下角出现一个消息框。该选项参数是一个配置对象，它包括：
                showType:定义消息框显示的模式，可选值包括:null,slide,fade,show.默认slide.
                showSpeed: 定义消息框完成显示的时间。默认600毫秒。
                width: 定义消息框的宽度。默认250。
                height:定义消息框的高度。默认100。
                msg:定义消息框显示的文本。
                title: 在消息框面板头部显示标题文本。
                timeout: 如果定义为0,消息框将不会自动关闭，除非用户手动关闭它。如果定义为非0值，消息框会在超时结束时自动关闭它。单位毫秒。
             2)alert:显示一个打印窗口。它的参数如下:
                title: 在头部显示标题文本。
                msg:显示文本内容。
                icon: 显示图标。可选值:error,question,info,warning。
                fn: 当窗口关闭后触发回调函数。
             3)confirm:显示一个包含确定和取消按钮的确认消息框。参数包括:
                title:在头部显示标题文本。
                msg: 显示文本内容。
                fn(b):回调函数，当用户点击OK按钮，返回True,才会处理该函数，其它按钮返回false,不处理。
             4)prompt:显示一个消息框，包含OK和Cancel按钮并提示用户输入一些文本。参数包括:
                title:在头部显示标题文本。
                msg:显示文本内容。
                fn(val):该回调函数处理用户输入的参数值。 
```         
10. .pagination         生成一个页码工具条。
```txt
           属性如下：
            1)total:当分页条创建后设置的记录数。默认1。
            2)pageSize:页面大小。默认10。
            3)pageNumber:当分页创建后显示的页码。默认1。
            4)pageList:用户能更改页面的大小。您也可以改变该属性定义的默认大小。默认[10,20,30,50]。
            5)loading:定义是否正在加载。默认false。
            6)buttons:定义自定义按钮，每个按钮都包含两个属性:
               iconCls: 该CSS类将显示一个背景图标。
               handler: 当按钮点击时触发一个处理函数。
            7)beforePageText:当输入组件前显示一个标签文本。
            8)afterPageText:当输入组件后显示一个标签文本。
            9)displayMsg:显示一个页面信息。
           方法如下:
            1)onSelectPage:当用户选择一个新页面时激活。该回调函数包括两个参数:
               pageNumber: 该新页面的页码。
               pageSize:该新页面的大小。
```
## EasyUI常见用法

1. 使用 data-options 来初始化属性。

data-options是jQuery Easyui 最近两个版本才加上的一个特殊属性。通过这个属性，我们可以对easyui组件的实例化可以完全写入到html中，例如：

```html
<div class="easyui-dialog" style="width:400px;height:200px"
    data-options="title:'My Dialog',collapsible:true,iconCls:'icon-ok',onOpen:function(){}">
    dialog content.
</div>
```

属性，事件，都可以直接写在data-options里面，这样就方便多了。

2.  tools定义工具栏，继承自panel的应该都可以使用。


```js
$('#p').panel({   
    width:500,   
    height:150,   
    title: 'My Panel',   
   tools: [{   
     iconCls:'icon-add',   
     handler:function(){alert('new')}   
    },{   
     iconCls:'icon-save' 
     handler:function(){alert('save')}   
   }]   
});
```

tools 同样可以加到data-options里面。

3.  easyui 里面的组件属性，同样可以写在标签里面。


```html
<div id="p" class="easyui-panel" title="My Panel" style="width:500px;height:150px;padding:10px;background:#fafafa;"  
          iconCls="icon-save"  closable="true"  
          collapsible="true" minimizable="true" maximizable=true>  
      <p>panel content.</p>  
      <p>panel content.</p>  
</div>
```

data-options和这里效果是一样，但是API里面大部分是按照属性来定义标签的，就想早先的HTML，而data-options就想style定义样式，不知道这两种有什么优劣。



## 继承
```js
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script src="easyui/jquery-1.7.2.min.js" type="text/javascript"></script>
    <script src="easyui/jquery.easyui.min.js" type="text/javascript"></script>
    <link href="easyui/themes/gray/easyui.css" rel="stylesheet" type="text/css" />
    <link href="easyui/themes/icon.css" rel="stylesheet" type="text/css" />
    <script type="text/javascript">
        $(function () {
            $('#dd').dialog({
                title: "My Dialog",
                modal: true, //dialog继承自window，而window里面有modal属性，所以dialog也可以使用
                collapsible: true, //是否可折叠，默认false
                minimizable: false, //是否可最小化，默认false
                maximizable: true, //是否可最大化，默认false
                resizable: true, //是否可折叠，默认false
                toolbar: [{
                    iconCls: 'icon-add',
                    handler: function () { alert('new') }
                }, {
                    iconCls: 'icon-save',
                    handler: function () { alert('save') }
                }],
                buttons: [{
                    iconCls: 'icon-add',
                    handler: function () { alert('new') }
                }, {
                    iconCls: 'icon-save',
                    handler: function () { alert('save') }
                }],
                //继承自panel,tool只有下面两个属性
                tools: [{
                    iconCls: 'icon-add',
                    handler: function () { alert('new') }
                }, {
                    iconCls: 'icon-save',
                    handler: function () { alert('save') }
                }]
            });
        })
    </script>
</head>
<body>
    <div id="dd" style="width: 500px; height: 400px;">
        Dialog Content.
    </div>
</body>
</html>
```


![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqj5aochgoj309k09kmwz.jpg)
<b><center>扫描关注：热爱生活的大叔</center>
<b><center><font size="2">（<font size="2" color="#FF0000">转载本站文章请注明作者和出处</font> <font size="2" color="#0000FF">热爱生活的大叔-uniquezhangqi</font><font size="2">）</font>
