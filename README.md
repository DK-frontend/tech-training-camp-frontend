tech-training-camp-frontend
本项目是一个在线Markdown编辑器

请务必阅读开放源代码许可证.

MarkDown
本项目使用vue框架去搭建的，主要设置左右两个区域，左边为textarea区域，右边为解析区域，
左边通过v-model实时绑定进行数据更新，右边通过v-html进行解析。
本项目使用了marked和highlight.js这两个库，用户输入到左边的内容会被传入到marked中解析为HTML格式，
然后传给右边区域v-html绑定的值，相应的插入代码会通过highlight进行高亮处理。